---
title: 小程序http封装
date: 2018-12-05
tags: ['小程序']
categories: 小程序
---

### 小程序wx.request请求封装
<!-- more -->

``` 
config.js
---
api_base_url:接口域名
appkey:头部携带的参数
const config = {
    api_base_url: '',
    appkey: "",
}

export {config }
```
```
http.js
---

import {config} from '../config.js'

定义状态码返回的错误信息 
const tips = {
    1: '抱歉，出现了一个错误',
    1005:'appkey无效，请前往www.7yue.pro申请',
    3000:'期刊不存在'
}

解构
class HTTP{
    默认GET请求方式，返回Promise
    request({url,data={},method='GET'}){
        return new Promise((resolve, reject)=>{
            this._request(url,resolve,reject,data, method)
        })
    }
    _request(url,resolve, reject, data={}, method='GET'){
        wx.request({
            url:config.api_base_url + url,
            method:method,
            data:data,
            header:{
                'content-type':'application/json',
                'appkey':config.appkey
            },
            success:(res)=>{
                const code = res.statusCode.toString()
                if (code.startsWith('2')){
                    resolve(res.data)
                }
                else{
                    reject()
                    const error_code = res.data.error_code
                    this._show_error(error_code)
                }
            },
            fail:(err)=>{
                reject()
                this._show_error(1)
            }
        })

    }

    _show_error(error_code){
        if(!error_code){
            error_code = 1
        }
        const tip = tips[error_code]
        wx.showToast({
            title: tip?tip:tips[1], 
            icon:'none',
            duration:2000
        }) 
    }


}

export {HTTP}
```


```
model.js
---
import {
    HTTP
}
from 'http.js'

ES6继承
class Model extends HTTP {

    GET请求
    getDetail(bid) {
        return this.request({
            url: `book/${bid}/detail`
        })
    }

    POST请求
    postComment(bid, comment) {
        return this.request({
            url: 'book/add/short_comment',
            method: 'POST',
            data: {
                book_id: bid,
                content: comment
            }
        })
    }
}
```

```
使用
---
import {
  Models
} from '/models.js'

实例化对象
const model = new Models()

model.getDetail().then(res => {

})
```