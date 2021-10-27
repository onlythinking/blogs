---
title: 前端-设备像素比和viewport(草稿)
date: 2021-10-27 15:34:10
tags: 
   - 前端
   - javascript
   - viewport
categories: 前端
copyright:
top: 1
typora-root-url: ../
typora-copy-images-to: ../images
---

## 视区(Viewport)

viewport 是用户网页的可视区域。视觉视区和布局视区。视觉视口是用户当前可见的布局部分。

物理像素和Css中的像素

> https://viewportsizes.com/

设备物理像素和设备独立像素的比例，也就是 **dpr = dp/ dips**。
 **dp(device pixels)：**设备物理像素；
 **dips(device-independent pixels)：**设备独立像素，dips = css像素/scale（缩放比例），所以dpr=(dp/css像素)*scale

计算一个css像素代表多少个物理像素



| 设备                           | **视区大小 （宽x高）** | **设备分辨率（宽x高）** |
| :----------------------------- | :--------------------- | :---------------------- |
| iPhone 12                      | 390 x 844              | 1170 x 2532             |
| iPhone 12 Mini                 | 360 x 780              | 1080 x 2340             |
| iPhone 12 Pro                  | 390 x 844              | 1170 x 2532             |
| iPhone 12 Pro Max              | 428 x 926              | 1248 x 2778             |
| iPhone SE                      | 214 x 379              | 640 x 1136              |
| iPhone 11 Pro Max              | 414 x 896              | 1242 x 2688             |
| iPhone 11 Xs Max               | 414 x 896              | 1242 x 2688             |
| iPhone 11                      | 414 x 896              | 828 x 1792              |
| iPhone 11 Xr                   | 414 x 896              | 828 x 1792              |
| iPhone 11 Pro                  | 375 x 812              | 1125 x 2436             |
| iPhone 11 X                    | 375 x 812              | 1125 x 2436             |
| iPhone 11 Xs                   | 375 x 812              | 1125 x 2436             |
| iPhone X                       | 375 x 812              | 1125 x 2436             |
| iPhone 8 Plus                  | 414 x 736              | 1080 x 1920             |
| iPhone 8                       | 375 x 667              | 750 x 1334              |
| iPhone 7 Plus                  | 414 x 736              | 1080 x 1920             |
| iPhone 7                       | 375 x 667              | 750 x 1334              |
| iPhone 6s Plus                 | 414 x 736              | 1080 x 1920             |
| iPhone 6s                      | 375 x 667              | 750 x 1334              |
| iPhone 6 Plus                  | 414 x 736              | 1080 x 1920             |
| iPhone 6                       | 375 x 667              | 750 x 1334              |
| iPad Pro                       | 1024 x 1366            | 2048 x 2732             |
| iPad Third & Fourth Generation | 768 x 1024             | 1536 x 2048             |
| iPad Air 1 & 2                 | 768 x 1024             | 1536 x 2048             |
| iPad Mini                      | 768 x 1024             | 768 x 1024              |
| iPad Mini 2 & 3                | 768 x 1024             | 1536 x 2048             |
| Nexus 6P                       | 411 x 731              | 1440 x 2560             |
| Nexus 5X                       | 411 x 731              | 1080 x 1920             |
| Google Pixel                   | 411 x 731              | 1080 x 1920             |
| Google Pixel XL                | 411 x 731              | 1440 x 2560             |
| Google Pixel 2                 | 411 x 731              | 1080 x 1920             |
| Google Pixel 2 XL              | 411 x 823              | 1440 x 2880             |
| Samsung Galaxy Note 5          | 480 x 853              | 1440 x 2560             |
| LG G5                          | 360w x 640             | 1440 x 2560             |
| LG G4                          | 360w x 640             | 1440 x 2560             |
| LG G3                          | 360w x 640             | 1440 x 2560             |
| One Plus 3                     | 480 x 853              | 1080 x 1920             |
| Samsung Galaxy S9              | 360 x 740              | 1440 x 2960             |
| Samsung Galaxy S9+             | 360 x 740              | 1440 x 2960             |
| Samsung Galaxy S8              | 360 x 740              | 1440 x 2960             |
| Samsung Galaxy S8+             | 360 x 740              | 1440 x 2960             |
| Samsung Galaxy S7              | 360 x 640              | 1440 x 2560             |
| Samsung Galaxy S7 Edge         | 360 x 640              | 1440 x 2560             |
| Nexus 7 (2013)                 | 600 x 960              | 1200 x 1920             |
| Nexus 9                        | 768 x 1024             | 1536 x 2048             |
| Samsung Galaxy Tab 10          | 800 x 1280             | 800 x 1280              |
| Chromebook Pixel               | 1280 x 850             | 2560 x 1700             |

> https://experienceleague.adobe.com/docs/target/using/experiences/vec/mobile-viewports.html?lang=zh
