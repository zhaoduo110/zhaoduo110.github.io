---
title: Vue首次加载过慢的问题解决
abbrlink: 90f26c7e
date: 2021-05-17 17:42:32
categories:
  - - Vue
tags:
  - JavaScript
  - Vue
  - Webpack
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218793.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218793.jpg
---



# 一： cdn引入js库（未验证）

把不经常改变的或第三方的js库放到 `index.html` 中，通过cdn引入，然后找到 `build/webpack.base.conf.js` 文件，在 `module.exports = { }`  中添加以下代码

```vue
externals: {
    'vue':'Vue',
    'echarts':'echarts',
    'element-ui':'ELEMENT',
},
```



# 二： 路由懒加载

```vue
const routers = [
    { path: '/', meta: { title: '首页' }, component: resolve => require([ './views/Home.vue' ], resolve) },
    { path: '/login', meta: { title: '登录' }, component: resolve => require([ './views/Login.vue' ], resolve) },
]

export default routers;
```

然后打包后就会发现，多了很多 1.xxxxx.js；2.xxxxx.js 等等，而 vendor.xxx.js 没了，剩下app.js 和manifest.js，而且app.js还很小



# 三：取消生成map文件（未验证）

找到 `config/index.js` ，修改下面 `productionSourceMap` 为 false，就行了



# 四：按需引入css和js（未验证）

不常用的 css和js不要在 `main.js` 里面引入，防止 `vendor.js` 和 `app.css` 文件过大



# 五：各个环境配置

在其他环境的配置文件首行加入 `NODE_ENV=production` 





# 六： 打包的css和js文件进行压缩

### 安装插件

```shell
npm install --save-dev compression-webpack-plugin
```

注意：各个插件依赖的webpack版本可能不一样会导致项目打包和运行失败

我的环境

```
webpack 5.37.0
webpack-cli 4.7.0
webpack-dev-server 3.11.2
```

使用的版本 `"compression-webpack-plugin": "^4.0.0"` 

使用 `npm install compression-webpack-plugin@4.0.0 -D`  可以降低插件版本



### 修改配置

修改`vue.config.js`文件

```vue
...
// gzip压缩插件
const CompressionWebpackPlugin = require('compression-webpack-plugin')


module.exports = {
    lintOnSave: true,
    devServer: {
        ...
    },
    chainWebpack: config => {
        ...
    },
    //以下为关键
    configureWebpack: {
        plugins: [
            new CompressionWebpackPlugin({
                filename: '[path].gz[query]',
                algorithm: 'gzip',
                test: /\.js$|\.html$|\.css$/,
                threshold: 4096,
                minRatio: 0.8,
            }),
        ],
    },
};

```

然后再进行打包时会发现会生成 .gz 后缀的文件，浏览器如果支持g-zip 会自动查找有没有gz文件，如果有的话则加载gz文件然后本地解压，减少了网络带宽的压力，提高加载速度

注意：如果web服务器不支持gzip也无法达到效果



### web服务器支持

#### Nginx支持gzip

在http内添加

```nginx
http{
    # 开启和关闭gzip模式
    gzip on;
    gzip_static on;
    # gizp压缩起点，文件大于1k才进行压缩
    gzip_min_length 1k;
    # 设置压缩所需要的缓冲区大小，以4k为单位，如果文件为7k则申请2*4k的缓冲区 
    gzip_buffers 4 16k;
    # 设置gzip压缩针对的HTTP协议版本
    gzip_http_version 1.1;
    # gzip 压缩级别，1-9，数字越大压缩的越好，也越占用CPU时间
    gzip_comp_level 2;
    # 需要压缩的文件mime类型
    gzip_types text/plain application/javascript application/x-javascript text/javascript text/css application/xml;
    # 是否在http header中添加Vary: Accept-Encoding，建议开启
    gzip_vary on;
    # nginx做前端代理时启用该选项，表示无论后端服务器的headers头返回什么信息，都无条件启用压缩
    gzip_proxied expired no-cache no-store private auth;
    # 不启用压缩的条件，IE6对Gzip不友好，所以不压缩
    gzip_disable "MSIE [1-6]\.";
}
```

