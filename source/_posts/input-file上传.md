---
title: input-file上传
date: 2018-08-07 16:40:48
banner: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533643673254&di=3b14c9eb666e5798e90e8574d087d9c1&imgtype=0&src=http%3A%2F%2Fc.hiphotos.baidu.com%2Fimage%2Fpic%2Fitem%2F9a504fc2d562853587bafefd9cef76c6a6ef6397.jpg
thumbnail: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533643573891&di=3e7c709f7bc31bd53e52c58a9eaed09d&imgtype=0&src=http%3A%2F%2Fcdnq.duitang.com%2Fuploads%2Fitem%2F201505%2F16%2F20150516211813_MwWSd.jpeg
tags:
- js
- html
---

### 使用file进行文件上传

#### 原生属性以及方法
1. value(选择图片的地址,可以使用js修改,但是不能修改成无效值,比如null)
2. files(选择的文件列表)
3. change事件(value发送改变时触发,注意:与选择文件的弹窗确认取消无关)
4. input事件(同上,触发动作不同)

#### 问题
1. input 自带样式太丑,并且不易修改
2. 不能直接发送请求,并且携带参数

<!-- more -->

#### 解决办法
1. 使用button模拟input上传,废话不多说,直接上代码

```html
<div class="h5-upImg">
    <button class="wt-button" id="btn">上传图片</button>
    <input
        id="file"
        type="file"
        name="file"
        accept="image/*"
        class="wt-upload__input">
</div>
```

```css
    .h5-upImg {
        display: block;
        text-align: center;
        cursor: pointer;
        outline: 0;
        margin: 100px auto;
    }

    .wt-upload__input {
        display: none;
    }

    .wt-button {
        padding: 10px 20px;
        font-size: 14px;
        border-radius: 4px;
        background-color: #169bd5;
        border-color: #169bd5;
        display: inline-block;
        line-height: 1;
        white-space: nowrap;
        cursor: pointer;
        border: 1px solid #dcdfe6;
        color: #fff;
        -webkit-appearance: none;
        text-align: center;
        box-sizing: border-box;
        outline: none;
        -webkit-tap-highlight-color: rgba(0,0,0,0);

    }
```
```js
var btn = document.getElementById('btn');
var upload = document.getElementById('file');
btn.onclick = function (e) {
    upload.click();
    e.preventDefault(); //取消了button的默认上传操作
}
upload.onchange = function () {
    var file = upload.files[0];
    // 上传文件必须使用formdata,而且content-type不能自己设置
    var formData = new FormData();
    var querys = location.search.substr(1).split('&');
    var params = {};
    for (var i = 0, val; val = querys[i++];) {
        var temp = val.split('=');
        var key = temp[0];
        var value = temp[1];
        params[key] = value;
    }

    // 通过apped的形式携带参数,只支持Blob,file对象以及字符串,并且formdata兼容性为ie10+
    formData.append('files', file);
    formData.append('s', params.s);

    ajax(formData);
}
```

注意:因为change是根据value来触发的,所以建议在请求的回调中清空files(`upload.value = ''`),否则会出现点击取消也会触发change

2. 如果需要本地预览图片怎么办?
    + `var url = URL.createObjectURL(file)`,可以将file对象转成本地地址
    + 将图片转成base64
    ```js
    function getBase64(img, callback) {
        const reader = new FileReader();
        reader.addEventListener('load', () => callback(reader.result));
        reader.readAsDataURL(img);
    }
    ```

#### 文档地址
1. https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input/file

