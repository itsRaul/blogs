---
title: 令牌无感知刷新
date: 2019-08-29 16:45:13
tags: ['JavaScript']
categories: JavaScript
---

### 令牌无感知刷新
<!-- more -->

![image](/images/token.jpg)

### h5   
1. 把用户的账号密码保存在缓存里，从缓存中读取账号密码获取token

2. access_token和refresh_token双令牌
如果access_token过期，用refresh_token重新获取access_token。
如果refresh_token也过期的情况下，每次获取access_token,同时刷新refresh_token,解决refresh_token过期时间

### 小程序
1. 小程序启动时，验证令牌是否合法，合法不做任何处理，不合法发放新的令牌

2. 正常情况下，正常携带令牌。令牌无效，返回code值进行判断处理，重新获取令牌，接口请求进行二次重发

```
// # 解构
class HTTP {
    request({
        url,
        data = {},
        method = 'GET'
    }) {
        return new Promise((resolve, reject) => {
            this._request(url, resolve, reject, data, method)
        })
    }
    // 2小时
    // token1 1小时59分59秒 超过2小时
    // 退出 
    // 自动 无感知帮助他重新刷新令牌
    // 退出 短时间 二次重发机制
    _request(url, resolve, reject, data = {}, method = 'GET', noRefetch = false) {
        wx.request({
            url: config.api_base_url + url,
            method: method,
            data: data,
            header: {
                'content-type': 'application/json',
                Authorization: this._encode()
            },
            success: (res) => {
                const code = res.statusCode.toString()
                if (code.startsWith('2')) {
                    resolve(res.data)
                } else {
                    if (code == '403') {
                        if (!noRefetch) {
                            this._refetch(
                                url,
                                resolve,
                                reject,
                                data,
                                method
                            )
                        }
                    } else {
                        reject()
                        const error_code = res.data.error_code
                        this._show_error(error_code)
                    }
                }
            },
            fail: (err) => {
                reject()
                this._show_error(1)
            }
        })

    }

    _show_error(error_code) {
        if (!error_code) {
            error_code = 1
        }
        const tip = tips[error_code]
        wx.showToast({
            title: tip ? tip : tips[1],
            icon: 'none',
            duration: 2000
        })
    }

    _refetch(...param) {
        var token = new Token();
        token.getTokenFromServer((token) => {
            this._request(...param, true);
        });
    }

    _encode() {
        const token = wx.getStorageSync('token')
        const base64 = new Base64()
        const result = base64.encode(token + ':')
        return 'Basic ' + result
    }


}
```

```
class Token {
    constructor() {
        this.verifyUrl = config.api_base_url + 'token/verify';
        this.tokenUrl = config.api_base_url + 'token';
    }

    verify() {
        var token = wx.getStorageSync('token');
        if (!token) {
            this.getTokenFromServer();
        } else {
            this._veirfyFromServer(token);
        }
    }

    _veirfyFromServer(token) {
        var that = this;
        wx.request({
            url: that.verifyUrl,
            method: 'POST',
            data: {
                token: token
            },
            success: function (res) {
                console.log(res)
                var valid = res.data.isValid;
                if (!valid) {
                    that.getTokenFromServer();
                }
            }
        })
    }

    getTokenFromServer(callBack) {
        var that = this;
        wx.login({
            success: function (res) {
                wx.request({
                    url: that.tokenUrl,
                    method: 'POST',
                    data: {
                        account: res.code,
                        type: 100
                    },
                    success: function (res) {
                        wx.setStorageSync('token', res.data.token);
                        console.log(res)
                        callBack && callBack(res.data.token);
                    }
                })
            }
        })
    }
}
```

```
app.js

App({
    onLaunch: function () {
        const token = new Token()
        token.verify()
    }
})
```


