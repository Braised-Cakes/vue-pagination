# Vue Pagination

vue分页组件, 同时支持数据缓存

[Vue.js](http://vuejs.org/) (基于 2.1.0)

## demo

![](https://raw.githubusercontent.com/BraisedCakes666/vue-pagination/master/src/images/UYt8PdN54m.gif)

## 安装

```bash
npm install vuejs-pagination --save-dev
```

## 基本用法

```html
<body id="app">
    <pagination
        :page-num="pageNum"            //总页码， 必须
        :current-page="activePage"    //当前页， 必须
        @change="val=>currentPage = val">        //触发事件, 必须
    </pagination>
</body>
```

```javascript
import Pagination from 'vuejs-pagination'
new Vue({
    el: '#app',
    data:function() {
        return{
            pageNum : 1, //总页数， 默认1
            activePage : 1 //当前页， 默认1
        }
    }
})
```

## 高级用法

缓存功能

```html
<pagination
    :types="types"                 //该接口类型
    :cache-list="list"             //需要缓存的内容
    :page-num="pageNum"            //总页码
    :current-page="activePage"     //当前页
    v-on:change="change">        //页码变化触发事件
</pagination>
```

```javascript
//翻页后请求接口前， 先获取缓存数据， 如果没有， 再调用接口
methods: {
    change : function(){
        this.$nextTick(() => {
            const cache = this.$refs.page.getCache();
            if (cache) {
                this.list = cache;
                return;
            }
            $.ajax({
                url : 'xxx'
            })
        }
    }
}
```

# Options

Name        | Default | Required | Description
:---------- | :------ | :------- | :----------
page-num     | 1       | true     | 总页码
current-page | 1       | true     | 当前页
page-size    | 5       | false    | 显示几个页码，需大于3
go-btn       |  true       | false    | 是否展示跳页按钮
cache-list   |         | false    | 需要缓存的数据
types       |         | false    | 需要缓存数据的类型


# Emit

Name   | Params     | Required | Description
:----- | :--------- | :------- | :-------------
change | val(点击的哪页) | true     | 当点击其他页时，会触发该方法
