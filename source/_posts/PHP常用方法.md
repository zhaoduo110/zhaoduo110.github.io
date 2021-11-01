---
title: PHP常用方法
categories:
  - PHP
tags:
  - PHP
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1580638993-thbUPVEsR1.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1580638993-thbUPVEsR1.jpg
abbrlink: 1765cf2d
date: 2021-10-29 14:07:42
---



获取请求来源的真实IP

```php
if (!function_exists('get_client_ip')) {
    function get_client_ip() {
        $online_ip = '';
        if(getenv('HTTP_CLIENT_IP') && strcasecmp(getenv('HTTP_CLIENT_IP'), 'unknown')) {
            $online_ip = getenv('HTTP_CLIENT_IP');
        } elseif(getenv('HTTP_X_FORWARDED_FOR') && strcasecmp(getenv('HTTP_X_FORWARDED_FOR'), 'unknown')) {
            $online_ip = getenv('HTTP_X_FORWARDED_FOR');
        } elseif(getenv('REMOTE_ADDR') && strcasecmp(getenv('REMOTE_ADDR'), 'unknown')) {
            $online_ip = getenv('REMOTE_ADDR');
        } elseif(isset($_SERVER['REMOTE_ADDR']) && $_SERVER['REMOTE_ADDR'] && strcasecmp($_SERVER['REMOTE_ADDR'], 'unknown')) {
            $online_ip = $_SERVER['REMOTE_ADDR'];
        }
        $ip_arr = explode(',',$online_ip);
        $ip = $ip_arr[0];
        return $ip;
    }
}
```

