---
title: axios
date: 2019-04-17 20:32:49
tags: ["vue"]
categories: vue
---

### axios封装
<!-- more -->

```
request.js

import _axios from 'axios';
import qs from  'qs';

//新建一个 axios 实例
const axios = _axios.create({
    //当在开发模式下,baseURL等于null,使用本地的localhost,设置本地代理解决跨域
    baseURL:process.env.NODE_ENV=='development'?null:'http://xxx.xx.xx:8080/',
    transformRequest:[function(data){       
        data = qs.stringify(data);
        return data
    }],
    headers:{
        'Content-Type': 'application/x-www-form-urlencoded'
    },
    transformResponse: [function (data) {
        // 对 data 进行任意转换处理
        let _data = JSON.parse(data);
        return _data;
    }],
    withCredentials:true,  // 表示跨域请求时是否需要使用凭证
    timeout: 5000,  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
})
axios.defaults.withCredentials = true

// 添加请求拦截器
axios.interceptors.request.use(
    config => {  
        //在发送请求之前做些什么
        // if (token) {
        //     config.headers['X-Token'] = getToken()
        // }
        return config
    },
    error => {
        // 对请求错误做些什么
        console.log('err' + error) // for debug
        return Promise.reject(error)
    }
)

// 添加响应拦截器
axios.interceptors.response.use(
  response => {
    //对响应数据做点什么
    const res = response.data
    //如果自定义代码不是20000，则被判断为错误。统一处理接口报错
    if (res.code !== 20000) {
      return Promise.reject(res.message || 'error')
    } else {
      return response
    }
  },
  error => {
    //对响应错误做点什么
    console.log('err' + error) // for debug
    return Promise.reject(error)
  }
)
export default axios;
```

```
//api.js

import http from request.js

//get

export const getGet = ({id}) => {
    let params = { ID }
    return http.get('url',params)
}

//post
export const getPost = ({id}) => {
    let data = { ID }
    return http.post('url',data)
}
```
