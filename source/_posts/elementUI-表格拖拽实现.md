---
title: elementUI 表格拖拽实现
date: 2018-07-13 15:37:06
banner: https://blog.zhangruipeng.me/hexo-theme-icarus/gallery/math.jpg
thumbnail: https://blog.zhangruipeng.me/hexo-theme-icarus/gallery/math.jpg
tags:
- elementUI
- Vue
---

### elementUI table drag

[官网解决方案](https://jsfiddle.net/gozhg07a/84/)
1. 下载sortable.js(不能使用npm的方式加载,单独存放在一个文件夹中)
2. `import '@/assets/plugins/sortable.js'`
```js
    mounted() {
        const table = document.querySelector('.table .el-table__body-wrapper tbody');
        Sortable.create(table, {
            onEnd: ({newIndex, oldIndex}) => {
                const targetRow = this.tableData.splice(oldIndex, 1)[0]
                this.tableData.splice(newIndex, 0, targetRow)
            }
        })
    }
```