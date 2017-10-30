---
layout: post
title: "Android Environment Variables"
description: "Access to environment variables"
category: android
tags: []
---

```java
String path = Environment.getDataDirectory().getAbsolutePath();
String downloadCacheDir = Environment.getDownloadCacheDirectory().getAbsolutePath();
String externalStorageDir = Environment.getExternalStorageDirectory().getAbsolutePath();
String externalPublicStorageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_MUSIC).getAbsolutePath();
String rootDir = Environment.getRootDirectory().getAbsolutePath();
```
##输出：
```
dataDir: /data
downloadCacheDir: /cache
externalStorageDir: /storage/emulated/0
externalPublicStorageDir: /storage/emulated/0/Music
rootDir: /system
```
