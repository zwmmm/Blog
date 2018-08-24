---
title: '使用echarts词云'
date: 2018-08-24 10:32:24
thumbnail: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533643573891&di=3e7c709f7bc31bd53e52c58a9eaed09d&imgtype=0&src=http%3A%2F%2Fcdnq.duitang.com%2Fuploads%2Fitem%2F201505%2F16%2F20150516211813_MwWSd.jpeg
tags:
- canvas
---

最近做了一个可视化的项目,其中有词云的功能,翻便了echarts官网也没有找到相关的demo
最终在github上找到了关于词云的echarts包,由于缺少文档,所以在此记录下使用词云的一些步骤

1. 安装echarts以及词云包
```bash
npm i echarts echarts-wordcloud -S
```
<!-- more -->
2. 初始化echarts
```js
import echarts from 'echarts'
import 'echarts-wordcloud'

let myChart = echarts.init(document.getElementById('key-words'));
let option = {...};
myChart.setOption(option);
```

3. 配置option
```js
// 生成颜色,当然这一步时可选的,可以使用默认的配色方案,如果需要自定义配色的话可以按照我这种方式来
let baseColor = ['#ff9400', '#21dab2', '#05edfe', '#0292d4', '#fefefe', '#8e8e94'];
let color = [];
baseColor.forEach((item, index) => {
    for (let i = 0; i < index + 1; i++) {
        color.push(item)
    }
});

let option = {
    series: [
        {
            type: 'wordCloud', // type指定wordCloud 表示使用词云
            gridSize: 30, // 每个词之间的间隔大小
            sizeRange: [18, 50], // 最小的字体大小与最大的字体大小
            rotationRange: [0, 0], // 字体的旋转角度
            shape: 'square', // 整体的布局,square为菱形布局,circle圆形布局,pentagon五角布局
            textStyle: {
                normal: {
                    color: ({dataIndex}) => color[dataIndex]
                }
            },
            data: [{name: '王者荣耀', value: 1000}, {...}, {...}] // 会根据value的大小来排序
        }
    ]
}
```
最终的样子为
![](https://raw.githubusercontent.com/zwmmm/assets/master/images/1.png)

4. 按照某张图片的样子来布局
其实实现这个只需在option中配置maskImage
```js
var maskImage = new Image();
var option = {
    series: [ {
        type: 'wordCloud',
        sizeRange: [10, 100],
        rotationRange: [-90, 90],
        rotationStep: 45,
        gridSize: 2,
        shape: 'pentagon',
        maskImage: maskImage, // 在这里指定一张图片即可
        textStyle: {
            normal: {
                color: function () {
                    return 'rgb(' + [
                        Math.round(Math.random() * 160),
                        Math.round(Math.random() * 160),
                        Math.round(Math.random() * 160)
                    ].join(',') + ')';
                }
            }
        },
        data: [{name: '', value: 0}, {....}])
    } ]
};

maskImage.onload = function () {
    option.series[0].maskImage
    chart.setOption(option);
}
maskImage.src = './logo.png';
```

效果为
![](https://github.com/ecomfe/echarts-wordcloud/blob/master/example/word-cloud.png?raw=true)

#### 资料地址
1. [echarts-wordcloud](https://github.com/ecomfe/echarts-wordcloud)
2. [词云在线配置体验效果地址](http://gallery.echartsjs.com/editor.html?c=xBkVAKkIle)
