---
layout: post
title: "有效的显示bitmaps"
description: ""
category: android
tags: []
---

加载bitmaps是需要技巧，以下列出了几个理由：

* 移动设备往往系统资源吃紧，每一个APP，android设备只提供16M的内存。对于各种屏幕尺寸，虚拟机提供了所需要的最小的应用程序内存。应该在最小内存限制下来优化程序。然而，记住许多设备也配置了更高的内存。

* Bitmaps占用很大内存，尤其是照片等。Glaxy Nexus照片达到1592x1936像素(5M)，如果bitmap所用的配置是ARGB_8888(从2.3开始)，那么加载这个图片会占用大约19M的内存(1592x1936x4字节)，立刻就耗尽了单个应用的内存限制。

* Android程序UI迅速地频繁请求几个bitmaps加载，例如ListView，GridView，ViewPager通常立刻包含了多个bitmap，还有更多的屏外的潜在的加载来应付指尖滑动。

有效地加载大的bitmaps
=====================

1.  获取图像的原始尺寸

    BitmapFactory.Options options = new BitmapFactory.Options();
    options.inJustDecodeBounds = true;
    BitmapFactory.decodeResource(getResources(), R.id.myimage, options);
    int imageHeight = options.outHeight;
    int imageWidth = options.outWidth;
    String imageType = options.outMimeType;

2.  加载缩小的版本到内存

    现在已知图像的尺寸，现在决定是否需要加载完整尺寸的图像，还是加载缩小的尺寸。考虑以下因素：

    * 估计原始图像占用的内存

    * 屏幕尺寸

        public static int calculateInSampleSize(
                    BitmapFactory.Options options, int reqWidth, int reqHeight) {
            // Raw height and width of image
            final int height = options.outHeight;
            final int width = options.outWidth;
            int inSampleSize = 1;

            if (height > reqHeight || width > reqWidth) {

                final int halfHeight = height / 2;
                final int halfWidth = width / 2;

                // Calculate the largest inSampleSize value that is a power of 2 and keeps both
                // height and width larger than the requested height and width.
                while ((halfHeight / inSampleSize) > reqHeight
                        && (halfWidth / inSampleSize) > reqWidth) {
                    inSampleSize *= 2;
                }
            }

            return inSampleSize;
        }

    根据inSampleSize解码

        public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId,
                int reqWidth, int reqHeight) {

            // First decode with inJustDecodeBounds=true to check dimensions
            final BitmapFactory.Options options = new BitmapFactory.Options();
            options.inJustDecodeBounds = true;
            BitmapFactory.decodeResource(res, resId, options);

            // Calculate inSampleSize
            options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);

            // Decode bitmap with inSampleSize set
            options.inJustDecodeBounds = false;
            return BitmapFactory.decodeResource(res, resId, options);
        }

    调用：
        mImageView.setImageBitmap(
            decodeSampledBitmapFromResource(getResources(), R.id.myimage, 100, 100));



在非UI线程处理bitmaps
=====================
