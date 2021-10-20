---
title: Swoft-踩坑
tags:
  - PHP
  - Swoft
categories:
  - - PHP
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218952853.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218952853.jpg
abbrlink: 68c1a07d
date: 2021-09-16 10:36:28
---



### JsonHelper::decode 不建议使用

使用php的json_decode()比较保险

遇到的问题：swoft做一个日志上报的功能，前端js对数据进行 bzip2(base64_encode(json())) 处理，后端进行相应的反处理，在进行json解析的时候使用swoft的 `JsonHelper::decode` 会报错，使用php的json_decode不会

报错信息

```
PHP Fatal error:  Uncaught InvalidArgumentException: json_encode error: Malformed UTF-8 characters, possibly incorrectly encoded in /var/www/company/product/dsom/dlarms-plus/vendor/swoft/stdlib/src/Helper/JsonHelper.php:56
```

有问题的json字符串：

```json
[{"$log_type":"webrtc","$log_second_type":"trace_log","$timestamp":1624438030906,"$uname":"","pay_code":"","localStorage":"{\"vuex\":{\"common\":{\"user\":{\"username\":\"zhaoduo\",\"token\":\"aaaaaddddddd\"}},\"goods\":{\"goodList\":[],\"good\":{},\"shopList\":[],\"shopDetail\":{}},\"time\":{\"smsCaptchaCountDown\":0,\"vmsCaptchaCountDown\":0}}}","sessionStorage":"{\"param\":{},\"app\":{},\"confirm_time\":null,\"is_first_into\":null,\"flag\":null,\"href_time\":null,\"access_token\":\"h5:4202dcfccbd2a82f4e9b813afd3593a33cd345a9\",\"token\":null}","data":"{\"info1\":\"ޥe¤-2-tryReconn\"}"}]
```

