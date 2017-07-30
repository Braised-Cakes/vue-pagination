<style>
body {
    display: flex;
    justify-content: center;
}
.content{
	height: 360px;
}
.category-list {
    display: flex;
    width: 500px;
    margin-bottom: 20px;
}

.category-list li {
    color: #76838f;
    font-size: 12px;
    margin: 15px 30px 0 0;
    cursor: pointer;
}

.category-list li.active {
    color: #08a1ef;
}

.music-list {}

.music-list li {
    height: 30px;
    padding: 0 10px;
    font-size: 12px;
    line-height: 30px;
    color: #666;
    cursor: pointer;
}

.loading {
    padding-top: 100px;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.loading img {
    width: 32px;
    height: 32px;
}

.loading p {
    padding-top: 20px;
    color: #A3AFB7;
    font-size: 14px;
    font-weight: bold;
}
</style>

<template>

<div id="app">
	<!--    不同的音乐类型     -->
	<div class="content">
        <ul class="category-list">
            <li @click="changeCategory(index)" :class="{active : index == category.index}" v-for="(i, index) in category.list">{{i.name}}</li>
        </ul>
        <!--    数据列表     -->
        <ul v-if="loadStatus == 'success'" class="music-list">
            <li v-for="(i, index) in musicList">{{i.name}}</li>
        </ul>
        <!--    loading，  加载时出现     -->
        <div class="loading" v-if="loadStatus == 'none'">
            <img src="./images/loading.gif" />
            <p>加载中，请稍后</p>
        </div>
	</div>
    <Page ref="page" :cache-list="musicList" :types="category.list[category.index].name" :page-num="pageNum" :current-page="currentPage" @change="change"></Page>
</div>

</template>

<script>
import './css/reset.css'
import $ from 'jQuery'
import Page from './components/Page'

export default {
    name: 'app',
    components: {
        Page
    },
    data() {
        return {
			category: { //音乐类型
                index: 0,       //当前选择的类型索引
                list: [{
                    name: '安静',
                    category: 889905
                }, {
                    name: '欢快',
                    category: 889906
                }, {
                    name: '庄重',
                    category: 889908
                }]
            },
            musicList: [], //音乐数据列表
            pageNum: 1, //总页码
            currentPage: 1, //当前页
            loadStatus: 'none' // none 请求前  success 请求成功
        }
    },
	mounted() {
        //页面初始化后， 第一次请求数据
        this.getData();
    },
    methods: {
        //当切换音乐类型时， 页码重置为第一页， 并获取新数据
        changeCategory(index) {
            this.category.index = index;
            this.currentPage = 1;
            this.getData();
        },
        //获取数据的方法
        getData() {
            //如果需要缓存数据， 就需要在$nextTick()下获取缓存数据
            this.$nextTick(() => {
                const cache = this.$refs.page.getCache();
                if (cache) {
                    this.musicList = cache;
                    return;
                }
                this.loadStatus = 'none';
                $.ajax({
                    url: 'http://store.eqxiu.com/api/product/cat/listProdByCate?attrGroupId=3&pageSize=10',
                    data: {
                        pageNo: this.currentPage,
                        category: this.category.list[this.category.index].category
                    },
                    success: (rs) => {
                        console.log(rs)
                        //为了看清缓存数据与没有缓存的区别， 特地延迟200ms后重置数据列表
                        setTimeout(() => {
                            this.musicList = rs.list;
                            this.pageNum = Math.ceil(rs.map.count / 10);
                            this.loadStatus = 'success';
                        }, 200)
                    }
                })
            });
        },
        //当点击其他页时， 会触发该方法，  设置currentPage为 点击的页码
        change(index) {
            this.currentPage = index;
            this.getData();
        }
    }
}

</script>
