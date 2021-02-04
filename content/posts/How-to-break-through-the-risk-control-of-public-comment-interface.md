---
title: "How to break through the risk control of Dazhongdianping's interface?"
date: 2021-02-04T09:44:05+08:00
categories: ["Python crawler"]
tags: ["Python", "crawler"]
draft: false
---

**Abstract**：在大众点评网的爬取过程当中，充满了大量的接口风控，对于敏感接口的请求均会要求携带`_token`参数，因此该参数的构造对于爬取地顺利进行极为重要。本文通过大众点评网的登录过程为例，详细介绍`_token`参数的生成逻辑，以实现正常爬取。

**Key words**：大众点评网&nbsp;&nbsp;&nbsp;&nbsp;爬虫&nbsp;&nbsp;&nbsp;&nbsp;_token参数&nbsp;&nbsp;&nbsp;&nbsp;selenium

# Introduction

大众点评网有着极为严格的反爬策略，除过常规的反爬手段之外，最重要的一环就是`_token`参数。具体而言，该参数可视为对请求风险的判别依据，其通过对document、window、screen、element对象以及自动化测试框架特征值等信息的综合判断，并融合请求体、查询字符串以及`URL`等数据生成`token`值，然后将该值作为请求体的一部分POST至后端，由后端完成解密从而达到识别风险的目的。

若请求由正常的浏览器发起（这意味着`_token`的值是正常的），则风险等级（riskLevel）为0，否则风险等级将会被提升到6（这意味着`_token`值生成错误或者非正常发起请求），将会进一步限制用户操作。被限制操作的用户若要继续操作，则需要通过滑动验证块的方式来解除高级别风控，但是根据笔者实际操作而言，滑动验证块仅仅在常规的反爬手段下有用（比如说请求过于频繁的情况），而对于高级别风控，即使验证块滑动成功也无法解除高风险等级限制，无论手动还是自动化操作。

由于`_token`的构造会考虑到自动化测试框架的特征值，这意味着诸如selenium之类的框架无用武之地，由该框架发起的请求riskLevel直接会被提升至6。有一种方法是通过中间人代理将对应js文件中的selenium特征值过滤或替换掉，从而规避被检测的风险。但是本文不采用该方法来处理。这是因为，一方面本次爬取数据量较大，而selenium速度较慢，故不适用于通过selenium来爬取；另一方面，即使过滤了selenium特征值，大众点评必然还会有其它的方法来识别它，所以该方法治标不治本。`_token`的生成还会考虑到document、window、screen、element等对象，并取其属性值，如果使用`requests`库来爬取，则无法读取该值，`_token`参数依然无法生成。这就形成了一个矛盾，两种办法都有缺陷，但是通过修改相应文件的js代码便可以解决requests库的使用导致无法读取相关属性值的问题。

此外，需要注意的是，大众点评网定义selenium特征值的`rohr.min.js`文件中，将其表示为了16进制，因此无法全局搜索。但在该文件开头定义了一个数组`_$_543c`，其中包含相关关键字及selenium的特征值序列，对其元素调用`.encode("utf-8")`方法可将其转化为ASCII字符串，从而方便过滤与替换。

基于上述，本文将会在构造生成`_token`参数的函数参数的基础上，对相应的js文件进行修改，然后通过pyexecjs驱动Node.js运行时执行之，最终获取`_token`值以实现顺利登录。

# API analysis

通过对大众点评登录流程进行追踪，可发现其完整的接口调用链如下（均使用POST方法）：

1. https://account.dianping.com
2. https://account.dianping.com/account/ajax/mobileVerifySend
3. https://account.dianping.com/account/ajax/mfastlogin

以用户通过手机号+验证码方式登录为例。首先，在用户输入手机号并点击发送验证码按钮的瞬间，浏览器会通过`/account/ajax/checkRisk`接口向服务器端发起请求以进行风控验证，请求体如下：

```json
{
    "riskChannel": "",
	"user": "",
    "_token": ""
}
```

其中，`riskChannel`的正常取值为202，取固定值即可；`user`为11位手机号；而`_token`就是B端对浏览器风险情况的一个预估计，也是本文的重点。服务器对此请求的响应体如下

```json
{
	"code":200,
	"msg":{
		"riskLevel":"",
		"publicKey":"",
		"uuid":""
	},
	"riskChannel":202
}

```

`code`为响应码，正常的请求一般为200；`riskLevel`为风险等级，正常情况下为0，异常情况下会被提升至6；publicKey参数暂时用不到，先不管；`uuid`参数为下一步请求中请求体的内容，该参数应该是服务器端用来标识某项请求是否经过了`/account/ajax/checkRisk`接口；最后，`riskChannel`的值正常情况下依旧返回202。

若上述riskLevel的值为0，则浏览器会进行下一项请求，即向用户手机发送验证码，如果不是0，则会出现类似于“当前操作异常”的提示，而无法进行验证码的发送。

假设当前环境正常，则可向`/account/ajax/mobileVerifySend`接口发起请求，请求体如下

```json
{
    "mobileNo": "",
    "uuid": "",
    "type": "304",
    "countrycode": "86"
}
```

`mobileNo`为11位手机号；`uuid`为`/account/ajax/checkRisk`接口的返回值之一；`type`取固定值304；`countrycode`为国家代码，固定为86。服务器对该请求的响应如下

```json
{
    "code":200,
    "mobileNo":"",
    "msg":{
        "info":"手机验证码已发送,请查看手机!"
    },
    "type":304,
    "uuid":""
}
```

`code`为响应码，发送成功为200，失败为100；`mobileNo`为11位手机号；`msg`为提示信息，发送成功的话msg对象为`info`字段，发送失败则为`err`字段；`type`应该是固定值，为304；`uuid`为请求时的`uuid`值。

得到验证码后，便可以通过`/account/ajax/mfastlogin`接口来执行登录动作。该接口的请求体如下

```json
{
    "mobile": "",
    "vcode": "",
    "channel": "0",
    "countrycode": "86",
    "type": "304",
    "keepMobile": "off",
    "_token": ""
}
```

其中，参数`mobile`为11位手机号；`vcode`为6位验证码；`channel`应该是固定值，为0；`countrycode`固定为86；`type`为304；`keepMobile`为是否记住手机号选项，记住为`on`，否则为`off`；最后一个参数`_token`也是对浏览器的风险估计。服务器对该接口的响应如下

```json
{
    "code":200,
    "msg":{
        "info":""
    }
}
```

若登录成功，则返回200状态码，`msg`对象中`info`为空，这是因为可以通过其余接口获取用户详细信息。

纵观上述对相关接口的分析，可以发现整个登录流程的核心就是参数`_token`的生成，接下来重点说明之。

# _token generation strategy

通过抓包获取所有的js文件，并全局搜索`_token`，发现该关键词存在于下述两个文件当中

1. https://www.dpfile.com/app/app-easy-login-frame/js/common.min.63556046f9f14a990d06e61e2afe0511.js
2. https://www.dpfile.com/app/app-easy-login-frame/js/login.min.ee92918871492df484315e5d4ea55a9b.js

进一步分析可以发现，`/account/ajax/checkRisk`接口的`_token`值定义在第一个js文件当中；`/account/ajax/mfastlogin`接口的`_token`值定义在第二个js文件当中。接下来对js文件继续分析

定位到第一个js文件的具体代码，容易发现存在这样一个函数

```javascript
    t.default = function(e, t) {
        if (!window.Rohr_Opt)
            return t;
        try {
            var n = [];
            for (var r in t)
                n.push(r + "=" + t[r]);
            var o = "?" + n.join("&")
              , i = Rohr_Opt.reload(e + o);
            return t._token = i,
            t
        } catch (e) {}
    }
```

很明显，该函数的主要作用就是构造`_token`，形参`t`为js对象，此处遍历`t`，然后将其用字符`&`拼接起来，再在拼接的结果前加上字符`?`，从而构造出参数`o`。容易推断出参数e应该为URI，从而`e + o`为URL，而`_token`的值则是通过函数`Rohr_Opt.reload()`构造的，其参数为`e + o`，即`_token`值初步依赖于`e + o`。我们可以进一步确认一下形参`e`和`t`的真实含义，继续寻找实现网络请求的代码，如下

```javascript
			200 == m.code && (1 == e.status ? o() : (r = h.host + "/account/ajax/checkRisk",
            i = "86" == d.countryCode ? d.mobile : d.countryCode + "_" + d.mobile,
            a = (0,
            u.default)(r, {
                riskChannel: d.riskChannel,
                user: i
            }),
            (0,
            l.default)({
                url: r,
                data: a,
                success: function(e) {
                    e && 200 == e.code ? 6 == e.msg.riskLevel ? (m.code = 100,
                    m.msg = e.msg.riskMessage,
                    v(m)) : 1 == e.msg.riskLevel ? (d.publicKey = e.msg.publicKey,
                    o()) : (m.publicKey = e.msg.publicKey,
                    m.uuid = e.msg.uuid,
                    v(m)) : (m.code = 101,
                    m.msg = "风控校验异常",
                    v(m))
                },
                error: function() {
                    m.code = 102,
                    m.msg = "风控服务请求出错",
                    v(m)
                }
            })))
```

显而易见，本文上述推断正确，实际参数r为URL作为形参e的实际值，a为对象作为形参t的实际值，然后传入函数当中构造`_token`。注意到`success`回调函数的逻辑：如果响应状态码code为200且风险等级riskLevel为6，则提示风险识别信息；若code为200且riskLevel为1则只设置publicKey；只有在riskLevel为其他值（包括0）的情况下设置uuid和publicKey以进行下一步操作。

接下来分析`Rohr_Opt.reload()`的代码，通过全局搜索`Rohr_Opt`，发现其存在于文件https://s0.meituan.net/mx/rohr/rohr.min.js中，打开该文件，发现其内部定义了匿名函数，继续寻找相应的代码，最终定位到下述代码

```javascript
                if (typeof (Rohr_Opt) === _$_543c[2]) {
                    iP.bindUserTrackEvent();
                    Rohr_Opt.reload = iP.reload;
                    Rohr_Opt.sign = iP.sign;
                    Rohr_Opt.clean = iP.decrypt
                }
```

至此，已经找到了最关键的函数，下一步就是对该js文件进行修改。先将该js文件拷贝至本地，由于该文件定义的是匿名函数，而我们要调用的是`Rohr_Opt.reload()`，因此先定义一个`Rohr_Opt`对象，这样的话匿名函数的执行就可以将`reload`函数设置给`Rohr_Opt`。

此外，由于该文件的运行依赖于document、window、screen、navigator等对象，因此需要在全局范围内定义之。对于document和window等对象，由于requests库未驱动浏览器，因此部分值需要直接在该js文件中修改，还要修改相应的函数，最后再js文档末尾定义下述函数即可。

```javascript
function gen_token(url){
    return Rohr_Opt.reload(url);
}
```

接下来就可以通过pyexecjs驱动Node.js运行时来执行该文件中的`gen_token()`函数来生成实际的`_token`值作为接口请求体的一部分。测试代码如下

```python
import execjs

token = execjs.compile(open(r"replace your js's path").read()).call('gen_token', 'https://account.dianping.com/account/ajax/checkRisk?riskChannel=202&user=replace your phone')
print(token)
```

执行接口`/account/ajax/mfastlogin`时`_token`的生成策略也一样，只需要更换Python代码中`call()`的第二个参数即可。

# Conclusion

本文较为详细地介绍了大众点评网的`_token`生成策略，通过修改对应js文件并以Python执行的方式来生成具体的`_token`值，可以有效突破大众点评网的接口风控，实现数据的正常爬取。缺陷在于通过pyexecjs驱动Node.js运行时执行js文件速度略慢，理想的方式是对`_token`生成技术进行逆向，这将会在下一篇文章中详细介绍，敬请关注。

修改后的rohr.min.js文件可以关注本公众号，并后台回复(rohr)获取。