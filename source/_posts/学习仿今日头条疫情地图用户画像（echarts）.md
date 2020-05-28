---
title: 学习仿今日头条疫情地图用户画像（echarts）
date: 2020-05-24 16:42:13
tags: 
	- echarts
categories: 前端
copyright: true
typora-root-url: ../
typora-copy-images-to: ../images
---

## Echarts Chartjs

最近由于公司图表展现需求较多，所以学习整理下相关资料。

前端图表框架也比较多，这里介绍两款。

- Apache Echarts
- Chartjs

Apache Echarts 涵盖各行业图表，满足各种需求，功能相当丰富。而后起之秀chartjs以其简单灵活特性，也深得开发设计人员喜爱。

>  https://echarts.apache.org （Echarts）
>
> https://www.chartjs.org (Charts)



两者都是开源项目，托管在Github

> https://github.com/apache/incubator-echarts
>
> https://github.com/chartjs/Chart.js

两者社区活跃简单对比

![Apache Echarts](/images/0_eh.png)



![Chartjs](/images/1_eh.png)



看上去Chartjs似乎更活跃。



## 疫情地图

使用echarts map 来实现。

```bash
npm install echarts --save
```

导入地图数据

ECharts 中提供了Javascript和Json两种格式的地图数据

> 从Github项目文件下载 https://github.com/apache/incubator-echarts/tree/master/map
>
> 从阿里提供地址下载  http://datav.aliyun.com/tools/atlas/#
>
> 百度网盘 链接: https://pan.baidu.com/s/1fHfW-qft_M58o2eQglwelw 提取码: 6fu5



项目代码片段

```javascript
echarts.registerMap("china", chinaMap);

series: [
            {
              name: '确诊数',
              type: 'map',
              mapType: 'china',
              roam: false,
              label: {
                show: true,
                color: 'rgb(63, 63, 63)'
              },
              data: actualData
            }
          ]
```

配置 visualMap

对照配置文档调整相应的参数即可，也很简单。

> https://echarts.apache.org/zh/option.html#series-map

疫情数据来源 《腾讯新闻》

![2_eh](/images/2_eh.png)



## 用户画像

仿照今日头条简版粉丝画像图。

![3_eh](/images/3_eh.png)



## IP来源查询

![4_eh](/images/4_eh.png)



## 小结

该篇主要是对Echarts map 整理。欢迎分享学习交流。

项目地址

> https://github.com/cuteJ/shop-server  (后端)
>
> https://github.com/cuteJ/shop-web-mgt （前端）



项目演示地址

> http://shop-web-mgt.onlythinking.com