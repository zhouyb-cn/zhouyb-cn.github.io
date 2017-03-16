---
title: CURL File
date: 2017-03-16 10:21:06
tags: curl laravel post
---

### 场景
* laravel 框架
* 上传图片需要通过API接口传到图片服务器上 

### 方案
采用curl的方式，在PHP版本大于5.5.0需要用到CURLFile类

``` php
function curlPost($url, $postData, $post = 1)
{
    $curl = curl_init();
    curl_setopt($curl, CURLOPT_POST, $post);

    curl_setopt($curl, CURLOPT_URL, $url);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($curl, CURLOPT_HEADER, 0);

    if ($postData) {
        curl_setopt($curl, CURLOPT_POSTFIELDS, $postData);
    }

    $data = curl_exec($curl);
    $httpcode = curl_getinfo($curl, CURLINFO_HTTP_CODE);
    curl_close($curl);
    if ($httpcode == 200) {
        return $data;
    } else {
        return $httpcode;
    }
}
```

在调用方法的时候第二个参数是个数组，API接口参数file
``` php
array(
	'file' => new \CURLFile($path)
)
```
若同时上传多个文件，如下
``` php
array(
	'file[0]' => new \CURLFile($path1),
	'file[1]' => new \CURLFile($path2)
));
```
