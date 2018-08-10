---
title: IntersectionObserverEntry
date: 2018-08-10 11:13:32
banner: http://s.qnc.shixunwang.net/FgDvKfQufgDfOXweiC2GSEp-Y-EF
thumbnail: https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSVqbb6RW4jIbMz0VeiatrjVi21gO8noc5P8yj3EJooC4g1dEXe
tags:
- js
---

### 使用IntersectionObserver实现懒加载以及无限加载

1. `IntersectionObserver`的介绍以及使用

`IntersectionObserver` api 用于观察dom元素是否可见
他的使用方法非常的简单
```js
const io = new IntersectionObserver(callback, option);
io.observe(element) // 观察元素
io.unobserve(element) // 停止观察
io.disconnect() // 关闭观察器
```
<!-- more -->
2. `IntersectionObserver`构造函数参数

callback 是一个回调函数,在元素刚刚进入视口以及刚刚离开视口的时候触发
callback函数的参数为一个数组,包含了所有被观察元素的IntersectionObserverEntry(可以理解为保存这个元素信息的一个对象)

|属性 |类型 |描述
|---- |---- |----
|isIntersecting |Boolen |true代表进入视口,false离开视口
|intersectionRatio |Number |目标元素的可见比例 0 代表完全不可见, 1代表完全可见
|target |element |目标元素

options 是一个配置对象

|属性 |类型 |描述
|---- |---- |----
|threshold |Array |表示intersectionRatio超过多少的时候触发callback默认值为[0]
|root |element |目标容器

3. 实现图片的懒加载
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        ul {
            margin: 0;
            padding: 0;
            outline: none;
            width: 100%;
            overflow: hidden;
        }
        li {
            min-height: 500px;
        }
    </style>
</head>

<body>
    <ul>
        <li>
            <img data-src="./images/1.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/2.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/3.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/4.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/5.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/6.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/7.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/8.jpg" alt="loading">
        </li>
        <li>
            <img data-src="./images/9.jpg" alt="loading">
        </li>
    </ul>
    <script>
        const { from } = Array;
        let imgs = document.querySelectorAll('img');
        let io = new IntersectionObserver(changes => {
            changes.forEach(({ target, isIntersecting }) => {
                if (!isIntersecting) return;
                target.setAttribute('src', target.dataset.src);
                target.onload = target.onerror = () => io.unobserve(target)
            })
        })
        from(imgs, item => io.observe(item));
    </script>
</body>

</html>
```
需要注意的是,需要提前给li设定一个高度,不然在图片未加载的时候 所有的img元素都在可视区,会导致所有的src都会别加上

4. 实现无线滚动
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        ul {
            margin: 0;
            padding: 0;
            outline: none;
            width: 100%;
            overflow: hidden;
        }

        .load-more {
            text-align: center;
            color: red;
        }
    </style>
</head>

<body>
    <ul>
        <li>
            <img src="./images/1.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/2.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/3.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/4.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/5.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/6.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/7.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/8.jpg" alt="loading">
        </li>
        <li>
            <img src="./images/9.jpg" alt="loading">
        </li>
    </ul>
    <div class="load-more">到底了</div>
    <script>
        let ul = document.querySelector('ul');
        let more = document.querySelector('.load-more');
        let io = new IntersectionObserver(changes => {
            const { target, isIntersecting } = changes[0];
            isIntersecting && loadMore()
        })
        io.observe(more)
        function loadMore() {
            let temp = new Array(9).fill();
            let fragment = new DocumentFragment();
            temp.map((item, index) => {
                let li = document.createElement('li');
                let img = new Image();
                img.src = `./images/${index + 1}.jpg`
                img.setAttribute('alt', 'loading');
                li.appendChild(img);
                fragment.appendChild(li);
            })
            ul.appendChild(fragment)
        }
    </script>
</body>

</html>
```

5. Polyfill
由于这个api比较新,浏览器兼容性不足,项目中使用需要引入Polyfill
地址: https://github.com/w3c/IntersectionObserver/tree/master/polyfill
使用方法:
`npm install intersection-observer --save`
`require('intersection-observer')`入口文件引入, 最好在第一行


6. 源码地址: https://github.com/zwmmm/example/tree/master/IntersectionObserver

7. 参考文章: 
    - https://github.com/justjavac/the-front-end-knowledge-you-may-not-know/issues/10
    - http://www.ruanyifeng.com/blog/2016/11/intersectionobserver_api.html
