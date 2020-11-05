---
title: Promise方法串行执行, 让Promise一个接一个进行
date: 2020-10-23 17:23:05
categories: 前端
tags: [js, Promise, Reduce]
---
# Promise方法串行执行, 让Promise一个接一个进行

## 转载来自：[参考文章](https://www.sheng00.com/2190.html)

本人在实际工作中遇到需要`一个接一个导出数据`的场景，在此记录以下方法

>控制台运行下面代码可以看到效果

```js
function waitPromise(i) {
    console.log(222, i)
    return new Promise((resolve, reject) => {
       setTimeout(() => {
            resolve('resolved');
        }, 1000);
    });
}

var ps = [];
for (let i = 0; i < 9; i++) {
    ps.push(() => waitPromise(i + 1 * 1000));
}
var p = Promise.resolve();

ps.reduce((p, c) => {
    return p.then(c)
}, p).then(() => {
    console.log('all finished');
}).catch(reject => {
    console.log('reject', reject)
});
```

>知识点：
> [Reduce](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 
> [Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 

