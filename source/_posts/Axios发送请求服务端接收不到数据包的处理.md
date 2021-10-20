---
title: Axios发送请求服务端接收不到数据包的处理
abbrlink: e4bc9495
date: 2021-05-08 10:04:33
categories:
  - - Vue
tags:
  - JavaScript
  - Vue
  - Axios
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218793.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218793.jpg
---



# POST表单数据的编码方式

### post提交数据的四种编码方式

#### text/plain;charset=UTF-8

即： 数据可以为  “aaaa” , “[12313],{dsafdsafsd:12313}”



#### application/x-www-form-urlencoded;charset=UTF-8

最终请求的数据包必须以key=value的方式存在，以便区分参数，于是键值对的概念出现了。

格式：键值对以 & 隔开, 如：  username=1111&password=2222

提交的时候，将 获取所有 input 的键值对，形成  `data: "username=1213&password=321"`  发送到了后台，后台接受到该字符串

`注意：正常的application/x-www-form-urlencoded是上面的键值对格式，ajax的处理方式与其他的不同`



>  随着互联网发展，数据传输越来越复杂，form已经不足以满足需求，于是ajax出现了，并且 首先实现了 form 表单提交的功能

这种编码方式数据传输仍然为 键值对格式，但是在 书写方式上， data以 Object 的形式 书写,内部实现将 Object 转为 字符串键值对

这应该是最常见的post编码方式，一般的表单提交默认以此方式提交。大部分服务器语言对这种方式都有很好的支持。在PHP中，可以用 `$_POST["key"]` 的方式获取到key的值



#### application/json;charset=UTF-8

> 又发展了一段时间， 出现了 一个新的表述 key-value字符串数据的的方式，称之为 JSON，符合JSON格式的字符串 也让服务器 很方便的获取参数。

Ajax 又实现了该种传参，于是 ajax的 `data:{username: 1213, password: 321} ` 最终传输时被 `JSON.stringify(data) =>  "{"username": 1213, "password": 321}"`，而服务器，则需要使用 json解析出传输的数据包 拿到 data Object

浏览器以 requestPayload 体现



#### multipart/form-data;charset=UTF-8

> 又发展了一段时间，简单的字符串传输也不满足了，想传输文件，word，excel，txt，图片等，即文件流传输。于是提出 一种数据内容格式，规定数据以 二进制 传输

浏览器以 FormData 体现。



### 问题分析

Axios默认传输时用的是 `application/json;charset=UTF-8` 编码方式，Header里面的 Content-type 设置的就是 application/json;charset=UTF-8

而此时如果服务端还是和处理其他编码方式的数据包一样来处理的话就会拿不到数据，编程语言接收表单数据都有自己通用或者底层的处理方式，但是通常情况下，编程语言也可以获取到请求的原始数据包，可以进行针对性的解析处理

出现后端拿不到请求数据的情况的话，可以前端处理也可以后端进行处理，`后端处理的话就是根据请求的 Content-type 来调整接收解析请求数据的方式`

前端处理的话 ，`不仅仅需要修改Header中的Content-type 为 application/x-www-form-urlencoded，请求的数据格式也需要处理为对应的编码格式才对` 。vue-axios-plugin 对这种处理也有说明：[参考](http://www.axios-js.com/zh-cn/docs/vue-axios-plugin.html#TODO)



### 提供两种Axios处理的方式

**第一种：单独对每个请求处理。在每次发送请求时设置Header和对请求数据进行转换**

```javascript

this.$axios({
    method:"post",
    url:"/api/haveUser",
    data:{
        name:this.name,
        password:this.password
    },
    /**
    *	 以下两个设置是重点
    **/
    // 设置Content-Type
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8'
    },
    //对表单数据进行转换
    transformRequest: [
        function(data) {
            var ret = ''
            for (var it in data) {
                ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&'
            }
            ret = ret.substring(0, ret.lastIndexOf('&'))
            return ret
        }
    ],
}).then((res)=>{
    console.log(res.data);
})
```



**第二种：全局处理**

在全局axios实例的请求拦截器中对post请求做处理，添加

```js
config.headers['Content-Type'] = 'application/x-www-form-urlencoded'
config.data = qs.stringify(config.data)
```

axios.js 

```js
import axios from 'axios'
import qs from 'qs'

// 创建一个 axios 实例
const service = axios.create({
    baseURL: "/api",
    timeout: 5000, // 请求超时时间
    withCredentials: false,
    crossDomain: false
})
// 请求拦截器
service.interceptors.request.use(
    config => {
        if (config.method === 'post') {
            //这两行是关键
            config.headers['Content-Type'] = 'application/x-www-form-urlencoded'
            config.data = qs.stringify(config.data)
        }
        return config
    },
    error => {
        // 发送失败
        return Promise.reject(error)
    }
)
// 响应拦截器
service.interceptors.response.use(
    response => {
        // dataAxios 是 axios 返回数据中的 data
        const dataAxios = response.data

        // 这个状态码是和后端约定的
        const { code } = dataAxios
        switch (code) {
            case 200:
                // [ 示例 ] code === 200 代表没有错误
                return dataAxios.data
            default:
                // 不是正确的 code
                // errorLog(`${dataAxios.msg}`)
                break
        }
    },
    // 错误请求的处理
    error => {
        if (error && error.response) {
            switch (error.response.status) {
                case 400: error.message = '请求错误'; break
                default: break
            }
        }
        errorLog(error.message)
        return Promise.reject(error)
    }
)

export default {
    service
}
```

正常使用即可

```js
import _axios from "@/plugins/axios";
var service = _axios.service


export default {
    //获取RtcToken
    Login(data) {
        return service({
            url: '/v1/login',
            method: 'post',
            data: data
        })
    },

}
```





