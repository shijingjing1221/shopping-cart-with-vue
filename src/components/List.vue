<



s

<template>

<div class="device" id="page-list">
    <header><span class="header-title">商品列表</span></header>
    <div class="page">
        <div class="tab-wrap">
            <ul class="cate-tab">
                <li class="cate" v-bind:class="{'tab-active': cate_index === index}" v-for="(item, index) in cate" @click="toggleCate(index)">{{ item.des }}</li>
            </ul>
        </div>
        <ul class="filter-bar">
            <li class="filter-opt" v-bind:class="{'filter-active': filter_index === index, 'filter-price': item.method === 'sortPrice', 'price-down': price_isAsc, 'price-up': !price_isAsc}" v-for="(item, index) in sortMethods" @click="sortBy(item.method)">{{ item.name }}</li>
        </ul>
        <ul class="goods-list">
            <li class="goods-item" v-for="(item, index) in list">
                <div class="goods-img">
                    <img v-bind:src="item.img" v-bind:alt="item.name">
                    <div class="flag" v-if="item.ishot">热</div>
                </div>
                <div class="goods-info">
                    <p class="goods-title">{{ item.name }}</p>
                    <div class="goods-price">
                        <span>¥<b>{{ item.price }}</b></span>
                    </div>
                    <span class="des">{{ item.sales }}人付款</span>
                    <span class="save" @click="addToCart(item)">+</span>
                </div>
            </li>
        </ul>
        <div class="action-bar">
            <router-link to="/Cart">
                <div class="action-btn buy-btn">查看购物车({{cart.length}})</div>
            </router-link>
        </div>
    </div>
</div>

</template>

<script>

import vueresource from 'vue-resource'
export default {
    name: 'List',
    data() {
        return {
            cate_index: 0, // 当前分类项下标
            filter_index: 0, // 当前条件筛选项下标
            price_isAsc: false, // 价格是否升序
            selectedNumber: 0,
            cate: [{
                id: 0,
                des: '推荐'
            }, {
                id: 1,
                des: '母婴'
            }, {
                id: 2,
                des: '鞋包饰品'
            }, {
                id: 3,
                des: '食品'
            }, {
                id: 4,
                des: '数码家电'
            }, {
                id: 5,
                des: '居家百货'
            }],
            sortMethods: [{
                name: '综合排序',
                method: 'setList'
            }, {
                name: '销量优先',
                method: 'sortSales'
            }, {
                name: '价格',
                method: 'sortPrice'
            }],
            goods: [],
            list: []
        }
    },
    props: ['cart', 'checkAllFlagGlocal'],
    created: function() {
        this.loadData(this.setList);


    },
    mounted: function() {
    },
    methods: {


        /**
         * @method 设置商品列表
         */
        setList: function() {
            var self = this;
            this.list = this.goods.filter(function(item) {
                return self.cate_index !== 0 ? item.type === self.cate_index : item
            });
        },

        loadData: function(next) {
            var self = this;

            this.$http.get('static/data/goods.json').then(response => {
                // get body data
                self.goods = response.body;
                next();
            }, response => {
                next();
                // error callback
            });
        },

        /**
         * @method 切换分类
         * @param {Number} index 需要切换类目的数组下标
         */
        toggleCate: function(index) {
            this.cate_index = index;
            // 分类操作
            this.setList();
            // 价格排序状态保持不变
            var filterIndex = this.filter_index;
            (filterIndex === 2) && (this.price_isAsc = !this.price_isAsc);
            // 商品排序
            this.sortBy(this.sortMethods[filterIndex].method);
        },

        /**
         * 根据属性值进行升序或降序的比较器
         *
         * @method 属性比较器
         * @param {String} prop 属性名
         * @param {String} type 排序方法 (desc: 降序, asc: 升序)
         */
        compare: function(prop, type) {
            type = type || 'desc';

            var flag1, flag2;
            if (type === 'desc') {
                flag1 = -1;
                flag2 = 1;
            } else if (type === 'asc') {
                flag1 = 1;
                flag2 = -1;
            }

            return function(obj1, obj2) {
                var val1 = obj1[prop],
                    val2 = obj2[prop];

                if (val2 < val1) {
                    return flag1;
                } else if (val2 > val1) {
                    return flag2;
                } else {
                    return 0;
                }
            }
        },

        /**
         * @method 按销量排序
         */
        sortSales: function() {
            this.list.sort(this.compare('sales'));
        },

        /**
         * @method 按价格排序
         */
        sortPrice: function() {
            var type = this.price_isAsc ? 'desc' : 'asc';
            this.list.sort(this.compare('price', type));
            this.price_isAsc = !this.price_isAsc;
        },

        /**
         * @method 排序调度器
         * @param {String} method 方法名
         */
        sortBy: function(method) {
            this.filter_index = this.sortMethods.findIndex(function(item) {
                return item.method === method;
            });
            method = method || 'setList';
            method !== 'sortPrice' && (this.price_isAsc = false);
            this[method]();
        },

        addToCart: function(goods) {
            this.$emit('addcart', goods);
            this.$emit('checkallonly', false);
        }
    }
}

</script>
