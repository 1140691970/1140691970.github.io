---
title: uniapp开发企业微信h5页面
date: 2020-09-02 10:55:12
categories: 前端-微信
tags: [Vue, uniapp, 微信]
---
# uniapp开发企业微信h5页面

## 微信官方文档
* [企业微信](https://work.weixin.qq.com/api/doc/90001/90142/90594 "企业微信")
* [微信公众号](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Overview.html "微信公众号")
* [示例代码](https://github.com/1140691970/public-files/tree/master/Web/project/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1 '示例代码')

> 企业微信与微信公众号同理，如下以开发企业微信为例
## 页面授权
1. [构造网页授权链接](https://work.weixin.qq.com/api/doc/90001/90143/91120 "构造网页授权链接")

我们在app.vue
```vue
onLaunch() {
    this.$wechat.wxAuthorize()
},
```

查看`/common/jsweixin/wechat.js`文件

```vue
// 微信授权
const wxAuthorize = () => {
	// 已经授权登录过的就不用再授权了
	if (MAIN.app.vuex_logoInfo) return;

	let code = util.getQueryString('code')

	// 如果拿到code，可以调用授权接口，没有拿到就跳转微信授权链接获取
	if (!!code) {
		// 这里可以调用后台接口，授权
	} else {
		let appid = 'ww.........'; //ww开头的appid

		let uri = encodeURIComponent(window.location.href);

		let scope = 'snsapi_base'; // 静默授权
		// let scope = 'snsapi_userinfo'; // 获取用户信息

		let agentid = 1000006;  //agentid

		let authURL = `https://open.weixin.qq.com/connect/oauth2/authorize?appid=${appid}&redirect_uri=${uri}&response_type=code&scope=${scope}&state=10000#wechat_redirect`;
		window.location.href = authURL;
	}
}
```

通过这一步进入页面可以拿到code

查看`/pages/home.vue`文件

```vue
// 微信授权
onLoad() {
    // 获取到code就存入缓存，否则跳登录页面
    let code = this.util.getQueryString('code')
    try {
        if (code && code != '') {
            uni.setStorageSync('code', code);
        }
    } catch (e) {
        // error
    }

    if (!this.vuex_logoInfo) {
        uni.reLaunch({
            url: '/pages/src/login'
        })
    }
}
```

## JSSDK 初始化配置
> 注意，根据官方的规则，所有需要使用 JSSDK 的页面必须先注入配置信息，即每一个页面，如果想要使用 JSSDK 中的能力的话，都需要对该页面进行权限签名，获取对应的配置数据，该步骤由后台处理即可。

查看`/common/jsweixin/wechat.js`文件

```vue
// 获取vue实例
var MAIN = require('../../main.js')

//初始化sdk配置
const init_jssdk = (callback, url) => {
	let nonceStr = 'boss'
	let timestamp = dayjs('2019-01-25').unix()
    
    // 这里调用了后台接口，后台处理获取签名
	MAIN.app.$u.api.getSignature({
		url,
		timestamp,
		noncestr: nonceStr,
	}).then(res => {
		if (res.data) {
			wx.config({
				beta: true, // 必须这么写，否则wx.invoke调用形式的jsapi会有问题
				debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
				appId: 'ww.....', // 必填，企业微信的corpID
				timestamp: timestamp, // 必填，生成签名的时间戳
				nonceStr: nonceStr, // 必填，生成签名的随机串
				signature: res.data, // 必填，签名，见 附录-JS-SDK使用权限签名算法
				jsApiList: ['checkJsApi',
					'onMenuShareTimeline',
					'onMenuShareAppMessage',
					'getLocation',
					'closeWindow',
				] // 必填，需要使用的JS接口列表，凡是要调用的接口都需要传进来
			});
		}
		if (callback) {
			callback(res)
		}
	})
}
```

## JSSDK 使用

1. 创建`获取定位`与`关闭页面`方法
```vue
// 获取定位
const getLocation = (data, url) => {
	url = url ? url : window.location.href;
	if (!isWechat()) {
		return;
	}
	//每次都需要重新初始化配置，才可以进行分享  
	init_jssdk(signData => {
		console.log('signData==>', JSON.stringify(signData))
		wx.getLocation({
			type: 'wgs84', // 默认为wgs84的gps坐标，如果要返回直接给openLocation用的火星坐标，可传入'gcj02'
			success: function(res) {
				console.log('res==>', res)
			},
			fail: (res) => {
				console.log('获取信息失败res==>', res)
			}
		});
	}, url);
}

// 关闭当前网页窗口接口
const closeWindow = (url) => {
	url = url ? url : window.location.href;
	if (!isWechat()) {
		return;
	}
	//每次都需要重新初始化配置
	init_jssdk(signData => {
		wx.closeWindow();
	}, url);
}
```

2. 在`/pages/home.vue`文件中使用 `关闭页面`方法 
```vue
// 退出登录
logOut() {
    uni.showModal({
        title: '提示',
        content: '是否退出登录',
        success: res => {
            if (res.confirm) {
                this.$wechat.closeWindow()
            }
        }
    });
},
```

## 最后
> 还有很多需要配置的地方，比如域名啊，IP 白名单以及测试号的配置等等。快去后台配置吧😀

