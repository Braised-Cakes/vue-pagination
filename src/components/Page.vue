<style lang="scss">
.v-pagination {
    display: flex;
    align-items: center;
    a,
    span {
        color: #76838f;
    }
    .v-page {
        display: flex;
        li {
            min-width: 34px;
            line-height: 34px;
            background: #fff;
            text-align: center;
            font-size: 12px;
            padding: 0;
            border: 1px solid #ccd5db;
            margin: 5px;
            border-radius: 4px;
            a {
                display: block;
                width: 100%;
                height: 100%;
            }
        }
        li:hover {
            background: #f7f7f7;
        }
        li.active {
            background: #08a1ef;
            border-color: #08a1ef;
            a {
                color: #fff;
            }
        }
        li.disabled {
            a {
                color: #dddddd;
            }
        }
    }
    .btn-next,
    .btn-prev {
        font-size: 24px;
    }
    .v-select-page {
        display: flex;
        align-items: center;
        margin-left: 10px;
        span {
            font-size: 14px;
        }
        .v-pagination-ipt {
            box-shadow: inherit;
            border: 1px solid #ccd5db;
            padding: 2px;
            outline: none;
            border-radius: 3px;
            margin: 0 5px;
            height: 30px;
            width: 30px;
            line-height: 30px;
            text-align: center;
			transition: 0.3s;
			font-size: 12px;
        }
		.v-pagination-ipt:focus{
			border-color: #20a0ff;
		}
        a {
            font-size: 12px;
            width: 34px;
            line-height: 34px;
            text-align: center;
            outline: none;
            border: 1px solid #ccd5db;
            background: #fff;
            display: block;
            margin-left: 5px;
            border-radius: 4px;
        }
		a:hover{
			background: #08a1ef;
			color: #fff;
			border-color: #08a1ef;
		}
    }
}

</style>

<template>

<div class="v-pagination">
    <ul class="v-page">
        <li :class=" {disabled:currentPage == 1}">
            <a @click="select(1)" href="javascript:void(0);">首页</a>
        </li>
        <li class="btn-prev" :class="{disabled:currentPage == 1}">
            <a @click="select(currentPage - 1)" href="javascript:void(0);">‹</a>
        </li>
        <li :class="{active : currentPage == i }" v-for="(i, index) in pageList">
            <a @click="select(i)" href="javascript:void(0);">{{i}}</a>
        </li>
        <li class="btn-next" :class="{disabled:currentPage == pageNum}">
            <a @click="select(currentPage + 1)" href="javascript:void(0);">›</a>
        </li>
        <li :class="{disabled:currentPage == pageNum}">
            <a @click="select(pageNum)" href="javascript:void(0);">尾页</a>
        </li>
    </ul>
    <div v-if="goBtn" class="v-select-page">
        <span>到</span>
        <input ref="input" class="v-pagination-ipt" type='text' :value="currentPage" @input="watchInput">
        <span>页</span>
        <a @click="select($refs.input.value)" href="javascript:void(0);">确定</a>
    </div>
</div>

</template>

<script>

export default {
    props: {
        pageNum: {
            type: Number,
            required: true,
            default: 1
        },
        currentPage: {
            type: Number,
            required: true,
            default: 1
        },
        pageSize: {
            type: [Number, String],
            required: false,
            default: 5,
			validator(val){
				return val >= 3;
			}
        },
        cacheList: {
            type: Array
        },
        types: {
            type: String,
            default: 'none'
        },
		goBtn : {
			type : Boolean,
			default : true
		}
    },
    computed: {
        halfSize: function() {
            return parseInt(this.pageSize / 2);
        },
        pageList: function() {
            var me = this;
            if (this.currentPage <= this.halfSize) {
                return Array.from({
                    length: this.pageSize > this.pageNum ? this.pageNum : this.pageSize
                }, (v, k) => {
                    return k + 1
                });
            } else if (this.currentPage >= this.pageNum - this.halfSize) {
                return Array.from({
                    length: this.pageSize > this.pageNum ? this.pageNum : this.pageSize
                }, (v, k) => {
                    return ((this.pageNum - this.pageSize) > 0 ? (this.pageNum - this.pageSize) : 0) + (k + 1)
                });
            } else {
                return Array.from({
                    length: this.pageSize > this.pageNum ? this.pageNum : this.pageSize
                }, (v, k) => {
                    return this.currentPage + k - this.halfSize;
                });
            }
        },

    },
    data: function() {
        return {
            cache: {}
        }
    },
    watch: {
        cacheList: function() {
            if (!this.cache[this.types]) {
                this.cache[this.types] = {};
            }
            this.cache[this.types][this.currentPage] = this.cacheList;
        }
    },
    methods: {
        watchInput() {
                setTimeout(() => {
                    if (isNaN(Number(this.$refs.input.value))) {
                        this.$refs.input.value = this.currentPage;
                    }
                }, 500);
            },
            select: function(val) {
                if (val != this.currentPage && val >= 1 && val <= this.pageNum) {
                    this.$emit('change', val)
                }if (val >= 1 && val <= this.pageNum) {
                    this.$emit('change', parseInt(val));
                } else if (val > this.pageNum) {
					this.$refs.input.value = this.pageNum;
                    this.$emit('change', this.pageNum);
                } else {
                    this.$emit('change', 1);
                }
            },
            getCache: function() {
                return (this.cache[this.types] && this.cache[this.types][this.currentPage]) || undefined
            }
    }
}

</script>
