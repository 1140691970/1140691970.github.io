---
title: 项目实战|Fetch基础请求封装
date: 2021-01-09 14:49:00
categories: 前端-请求
tags: [js, Fetch]
---
# 项目实战|Fetch基础请求封装基础请求封装

>参考文章：
> [项目实战|基础请求封装](https://www.cnblogs.com/liuxianan/p/chrome-plugin-develop.html) --- 由于我是在泛微里编写代码，我在基础上稍微做了一些改变(Qs为cdn引入等)

## 封装

未封装之前的 fecth 如下使用

```js
fetch('https://www.baidu.com/search/error.html') // 返回一个Promise对象
.then((res) => {
    return res.text() // res.text()是一个Promise对象
})
.then((res) => {
    console.log(res) // res是最终的结果
})
```

封装完整代码如下

```js
/*
 * @Author: Cookie
 * @Date: 2020-06-22 19:26:03
 * @LastEditors: kang
 * @LastEditTime: 2021-01-09
 * @FilePath: /utils/fetchUtil/index.js
 * @Description: fetch请求封装
 */

var qs = Qs
ecodeSDK.imp(Storage);
class FetchUtil {
  constructor(config = {}) {
    const {
      cache = 'no-cache', // * default, no-cache, reload, force-cache, only-if-cached
      credentials = 'same-origin', // include, same-origin, *omit
      headers = {},
      mode = 'cors', // no-cors, cors, *same-origin
      redirect = 'follow', // manual, *follow, error
      referrer = 'no-referrer', // *client, no-referrer
      timeOut = 3000, // 超时时间
      BASE_URL = 'http://192.168.0.147:8080',
      requestType = 'JSON',
      cacheType = ''
    } = config

    this.FetchConfig = {
      cache,
      credentials,
      headers,
      mode,
      redirect,
      referrer,
    }

    this.config = {
      timeOut,
      BASE_URL,
      requestType,
    }

    this.cacheStorage = cacheType ? new Storage({ type: cacheType }) : ''

    this.dataOperation = {
      JSON: {
        headers: {
          'Content-Type': 'application/json; charset=utf-8', // 告诉服务器，我们提交的数据类型为 json 格式
        },
        formatting(params) {
          return JSON.stringify(params)
        }
      },
      FormData: {
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8' // 告诉服务器，我们提交的数据类型为 FormData 格式
        },
        formatting(params) {
          let _formData = new FormData();
          Object.keys(params).forEach(key => {
            _formData.append(key, params[key]);
          })
          return _formData
        }
      },
      FormData2: {
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8' // 告诉服务器，我们提交的数据类型为 FormData 格式
        },
        formatting(params) {
          var arr = []
          for (var x in params) {
            if (params[x]) {
              arr.push(x + '=' + params[x])
            }
          }
          return encodeURI(arr.join('&'))
        }
      }
    }
  }

  send({ url, FetchConfig }) {
    // 发送 ajax 请求
    const { BASE_URL, timeOut } = this.config
    const ajax = new Promise((resolve, reject) => {
      fetch(BASE_URL ? `${BASE_URL}${url}` : url, FetchConfig).then((response) => {
        if (response.ok) {
          return response.json()
        } else {
          reject({
            status: response.status,
            msg: response.statusText
          })
        }
      }).then((data) => {
        resolve(data)
      })
    })
    // 设置超时时间
    const time = new Promise((resolve, reject) => {
      setTimeout(() => {
        reject('time out')
      }, timeOut);
    })

    return Promise.race([ajax, time])
  }

  preSend({ url, params, headers, method }) {
    const { requestType } = this.config
    const FetchConfig = {
      ...this.FetchConfig,
      method,
      headers: {
        ...this.dataOperation[requestType].headers,
        ...headers
      },
    };
    if (!!params) FetchConfig.body = this.dataOperation[requestType].formatting(params);
    return this.send({
      url,
      FetchConfig
    })
  }

  // 封装请求
  get({ url, query, headers }) {
    const key = query ? `${url}?${qs.stringify(query)}` : url
    if (this.cacheStorage) {
      if (this.cacheStorage.getItem(key)) {
        return Promise.resolve(this.cacheStorage.getItem(key))
      } else {
        return this.preSend({ url: key, headers, method: 'GET' }).then(data => {
          this.cacheStorage.setItem(key, data)
          return data
        })
      }
    } else {
      return this.preSend({ url: key, headers, method: 'GET' })
    }
  }

  post({ url, query, params = {}, headers }) {
    return this.preSend({ url: query ? `${url}?${qs.stringify(query)}` : url, params, headers, method: 'POST' })
  }
}

ecodeSDK.exp(FetchUtil);
```

## 调用

```js
/*
 * @Author: Cookie
 * @Date: 2020-06-22 19:26:03
 * @LastEditors: kang
 * @LastEditTime: 2021-01-09
 * @FilePath: /utils/fetchUtil/fetchApi.js
 * @Description: 请求API模块
 */

ecodeSDK.imp(FetchUtil); //公共表格逻辑组件

const FetchApi = {
  
  //审批列表批量提交
  requestBatchSubmit: ( params) => {
    const fatch = new FetchUtil({requestType: 'FormData2'})
    return fatch.post({ url: '/api/workflow/reqform/requestBatchSubmit', params })
  },

  //获取审批表格数据
  getApprovalTableData: (url, params) => {
    const fatch = new FetchUtil()
    return fatch.post({ url, params })
  },
  
}

//发布模块
ecodeSDK.exp(FetchApi);
```

```js
ecodeSDK.imp(FetchApi); //公共API模块

let params = {
    remark: '',
}

FetchApi.requestBatchSubmit(params).then(res=>{
  message.success('提交数据成功');
}).catch(err=>{
  message.error('提交数据失败');
})
```
