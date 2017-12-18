<template>
  <div id="app">
    <router-view v-bind:cart="cart" v-bind:checkAllFlag="checkAllFlag" v-bind:selectedNum="selectedNum" v-on:addcart="AddToCart" v-on:checkall="checkAll" v-on:checkallonly="checkAllOnly" v-on:delgoods="delGoods" v-on:selectgoods="selectGoods"/>
  </div>
</template>

<script>

export default {
  name: 'app',
  data: function(){
    return {
      cart: [],
      checkAllFlag: false,
      selectedNum: 0
    }
  },
  methods:{
    AddToCart:function(goods){
      var alreadyIndex = this.cart.findIndex(function(item, index) {
          return item.id === goods.id;
      });

      // 如果不存在则添加
      if (alreadyIndex === -1) {
          var cartIndex = this.cart.length;
          // 添加新的商品，并初始化其数量、价格、被选中状态
          this.cart.push(goods);
          this.$set(this.cart[cartIndex], 'quantity', 1);
          this.$set(this.cart[cartIndex], 'subtotal', goods.price.toFixed(1));
          this.$set(this.cart[cartIndex], 'checked', false);
          // 新增商品，购物车不能为全选
          this.checkAllFlag = false;
          return;
      }

      // 如果商品已存在并且库存足够，数量加1
      var alreadyGoods = this.cart[alreadyIndex];
      var num = alreadyGoods.quantity,
          stock = alreadyGoods.stock;

      if (num < stock) {
          this.$set(alreadyGoods, 'quantity', ++alreadyGoods.quantity);
          this.$set(alreadyGoods, 'subtotal', (alreadyGoods.price * alreadyGoods.quantity).toFixed(1));
      }

    },
    checkAllOnly: function(value){
      this.checkAllFlag = value;
    },
    checkAll: function () {
    var self = this;
    this.checkAllFlag = !this.checkAllFlag;
    console.log(this.checkAllFlag, "in app.vue")

    this.cart.forEach(function (item) {
        if (self.checkAllFlag) {
            // 全选
            item.checked = true;
            self.selectedNum = self.cart.length;
        } else {
            // 取消全选
            item.checked = false;
            self.selectedNum = 0;
        }
    });
  },
  delGoods:function(){
    var cart = this.cart;
    this.cart = cart.filter(function(item) {
        return !item.checked
    });
    this.selectedNum = 0;
    this.checkAllFlag = false;
  },
  selectGoods: function(item) {
      item.checked = !item.checked;
      item.checked ? ++this.selectedNum : --this.selectedNum;
      // 设置全选
      this.selectedNum === this.cart.length ? this.checkAllFlag = true : this.checkAllFlag = false;
  }
  }
}
</script>

<style scoped>


</style>
