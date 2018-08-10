---
title: easy-mock的基本使用
date: 2018-07-13 17:51:09
thumbnail: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1533643573891&di=3e7c709f7bc31bd53e52c58a9eaed09d&imgtype=0&src=http%3A%2F%2Fcdnq.duitang.com%2Fuploads%2Fitem%2F201505%2F16%2F20150516211813_MwWSd.jpeg
tags:
- mock
- 前后端分离
---

### 使用步骤
1. https://easy-mock.com/注册账号密码
2. 新建一个项目 输入项目名称 以及项目的baseUrl 点击创建
3. 在项目中创建接口，填写请求方式以及请求路径（注意不要在写baseUrl）
4. 在代码中自己写好需要返回的值（JSON）格式
5. 复制接口地址 在项目中直接请求这个地址即可

### 使用思路
由于使用mock只是解决后端接口还未出来的 无数据可用的问题，所以等到与后端联调的时候我们需要关闭mock

1. 封装项目中的请求，提取请求的baseUrl 等到需要与后端联调时直接修改baseUrl即可
2. mock中的请求路径最好先询问后端，保证与真实api路径一致
3. 如果前端主导的项目，返回的数据格式也可以要求后端api在开发时参照你mock的数据格式

<!-- more -->
封装请求参考代码
```js
// ----fetach.js

import {baseUrl} from './env'
import {Message} from 'element-ui'

const fetch = require('isomorphic-fetch')

const codeMessage = {
    200: '服务器成功返回请求的数据。',
    201: '新建或修改数据成功。',
    202: '一个请求已经进入后台排队（异步任务）。',
    204: '删除数据成功。',
    400: '发出的请求有错误，服务器没有进行新建或修改数据的操作。',
    401: '用户没有权限（令牌、用户名、密码错误）。',
    403: '用户得到授权，但是访问是被禁止的。',
    404: '请求路径不存在',
    406: '请求的格式不可得。',
    410: '请求的资源被永久删除，且不会再得到的。',
    422: '当创建一个对象时，发生一个验证错误。',
    500: '服务器发生错误，请检查服务器。',
    502: '网关错误。',
    503: '服务不可用，服务器暂时过载或维护。',
    504: '网关超时。',
};

function checkStatus(response) {
    if (response.status >= 200 && response.status < 300) {
        return response.json();
    }
    const errortext = codeMessage[response.status] || response.statusText;
    const error = new Error(errortext);
    error.name = response.status;
    error.response = response;
    throw error;
}

export default function (query) {
    const {name, api, method, body} = query;
    let url = baseUrl + api;
    let options = {method: method || 'get'}
    if (body) {
        options.body = body;
    }
    return fetch(url, options)
    .then(checkStatus)
    .then(res => res)
    .catch(err => {
        Message({
            message: err.message,
            type: 'error',
            duration: 5000,
            center: true,
        })
    })
}
```

```js
// ---env.js
let baseUrl = '';

if (process.env.NODE_ENV === 'development') {
    // 你的easy-mock项目 baseUrl,联调时将这个地址替换成联调地址
    baseUrl = ' https://easy-mock.com/mock/5b45cb982a54ac783dfea68a/wetest';
}
else {
    baseUrl = '';
}

export {
    baseUrl,
}
```

具体可以看[项目源码](https://github.com/zwmmm/routerTpl/tree/master/src/config)

### mock数据怎么编写？
1. 最简单的模式，死数据！！！！
```
{
"description": "(aIEAk",
    "imgs": [
        {
            "url": "https://placem.at/people?w=90&h=160"
        },
        {
            "url": "https://placem.at/people?w=90&h=160"
        }
    ]
}
```
2. 使用模板语法
```
// 属性名   name
// 生成规则 rule
// 属性值   value
`'name|rule': value`
```

- 生成规则有七种
    +`'name|min-max': value`
    +`'name|count': value`
    +`'name|min-max.dmin-dmax': value`
    +`'name|min-max.dcount': value`
    +`'name|count.dmin-dmax': value`
    +`'name|count.dcount': value`
    +`'name|+step': value`

- 属性值是String
    + `'name|min-max': string`
    重复生成string，次数为最小min次最大max
    q: name|1-2: a ==> aa
    + `'name|count': string`
    通过重复 string 生成一个字符串，重复次数等于 count
    q: name|3: a ==> aaa

- 属性值是Number
    + `'name|min-max': number`
    生成一个大于等于 min、小于等于 max 的整数
    q: `name|1-10: 100 ==> 5` 不管属性值填了 结果都是遵循规则

- 属性值是Object
    + `'name|count': object`
    从属性值 object 中随机选取 count 个属性。
    q: `name|3: {a: 1, b: 1, c: 1, d: 1} ==> {a: 1, c: 1, b: 1}`
    + `'name|min-max': object`
    从属性值 object 中随机选取 min 到 max 个属性。

- 属性值是Array
    + `'name|1': array`
    从属性值 array 中随机选取 1 个元素，作为最终值
    + `'name|+1': array`
    从属性值 array 中顺序选取 1 个元素，作为最终值。
    + `'name|min-max': array`
    通过重复属性值 array 生成一个新数组，重复次数大于等于 min，小于等于 max。
    + `'name|count': array`
    通过重复属性值 array 生成一个新数组，重复次数为 count
    q: `name|3: [1, 2, 3] ==> [1, 2, 3, 1, 2, 3]`

3. 使用占位符
```JSON
{
  "string|1-2": "@string",
  "integer": "@integer(10, 30)",
  "float": "@float(60, 100, 2, 2)",
  "boolean": "@boolean",
  "date": "@date(yyyy-MM-dd)",
  "datetime": "@datetime",
  "now": "@now",
  "url": "@url",
  "email": "@email",
  "region": "@region",
  "city": "@city",
  "province": "@province",
  "county": "@county",
  "upper": "@upper(@title)",
  "guid": "@guid",
  "id": "@id",
  "image": "@image(200x200)",
  "title": "@title",
  "cparagraph": "@cparagraph",
  "csentence": "@csentence",
  "range": "@range(2, 10)"
}
```

===> 随机生成

```JSON
{
  "string": "&b(V",
  "integer": 29,
  "float": 65.93,
  "boolean": true,
  "date": "2013-02-05",
  "datetime": "1983-09-13 16:25:29",
  "now": "2017-08-12 01:16:03",
  "url": "cid://vqdwk.nc/iqffqrjzqa",
  "email": "u.ianef@hcmc.bv",
  "region": "华南",
  "city": "通化市",
  "province": "陕西省",
  "county": "嵊州市",
  "upper": "DGWVCCRR TLGZN XSFVHZPF TUJ",
  "guid": "c09c7F2b-0AEF-B2E8-74ba-E1efC0FecEeA",
  "id": "650000201405028485",
  "image": "http://dummyimage.com/200x200",
  "title": "Orjac Kwovfiq Axtwjlop Xoggxbxbw",
  "cparagraph": "他明林决每别精与界受部因第方。习压直型示多性子主求求际后世。严比加指安思研计被来交达技天段光。全千设步影身据当条查需府有志。斗中维位转展新斯克何类及拉件科引解。主料内被生今法听或见京情准调就品。同六通目自观照干意音期根几形。",
  "csentence": "命己结最方心人车据称温增划眼难。",
  "range": [2, 3, 4, 5, 6, 7, 8, 9]
}
```

### 具体参考官网
1. https://easy-mock.com/docs
2. https://github.com/nuysoft/Mock/wiki