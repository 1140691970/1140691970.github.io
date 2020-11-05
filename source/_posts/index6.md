---
title: Chrome插件(扩展)开发
date: 2020-11-05 10:27:00
categories: 前端-谷歌
tags: [Chrome]
---
# Chrome插件(扩展)开发

>参考文章：
> [【干货】Chrome插件(扩展)开发全攻略](https://www.cnblogs.com/liuxianan/p/chrome-plugin-develop.html) --- 较为详细的开发攻略
> [开发chrome extension抓取页面数据，并传输到另一页面](https://blog.csdn.net/m0_37729058/article/details/79485302)  --- 简单了解通讯
> [360安全浏览器开放文档](http://open.se.360.cn/open/extension_dev/getstarted.html) --- 360官网开发文档

> [示例代码](https://github.com/1140691970/public-files/tree/master/Web/project/crx%E6%8F%92%E4%BB%B6 '示例代码') --- 这里是我对照上面文章所创建的简单示例

## 安装
1.点击谷歌右上角按钮，接着在更多工具里找到拓展程序
2.确保右上角开发者模式打开，没打开先打开
3.点击左上角 `加载已解压的扩展程序`
4.选中当前crx文件夹即可
    
## 打包
1.找到拓展程序里面
2.点击左上角 `打包扩展程序`
3.扩展程序根目录 选中crx插件文件夹
4.点击打包拓展程序即可,会生成crx文件，后面只需要直接拖入crx文件就行了
