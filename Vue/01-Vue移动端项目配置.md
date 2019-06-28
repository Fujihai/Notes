### 引言

此文记录如何对 Vue 移动端项目进行配置。

### 配置一

全局 `index.html` 文件中配置 `meta` 标签属性，如下：

```html
<meta name="viewport" content="width=device-width,initial-scale=1.0,maxium-scale=1,user-scalable=no">
```

### 配置二

导入 `rem.js` 文件。

1. 在 `src` 目录下创建  `config` 文件， 添加 `rem.js` 文件。

> (function(){function a(){var b=document.documentElement.clientWidth;b=b>750?750:b;var c=b/750*100;document.getElementsByTagName("html")[0].style.fontSize=c+"px"}a();window.onresize=a})();

2. 在 `src/main.js` 中导入 `rem.js` 文件，如下

```
import './config/rem'
```

### 配置三

* 安装 `px2rem-loader` 插件

```javascript
npm install px2rem-loader
```

* `build` 文件夹下的 `utils.js` 中的 `generateLoaders` 函数上方添加以下配置

```javascript
//添加 rem 相关配置
var px2remLoader = {
    loader: 'px2rem-loader',
    options: {
        remUnit: 50
    }
};
```

> remUnit 的设置：将 index.html 网页拉伸到某一宽度，如375「375为设计稿的值」时，此时网页的 font-size 值则为 remUnit 的值。

* `generateLoaders` 函数中的 `loaders` 常量也需要进行配置

```javascript
 const loaders = options.usePostCSS ? [cssLoader, postcssLoader] : [cssLoader,px2remLoader];
```

