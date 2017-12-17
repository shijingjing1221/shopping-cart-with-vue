<template>
  <div id="app">
    <router-view v-bind:cart="cart" v-bind:checkAllFlagGlocal="checkAllFlag"/>
  </div>
</template>

<script>
export default {
  name: 'app',
  data: function(){
    return {
      cart: [],
      checkAllFlag: false
    }
  }
}
</script>

<style scoped>


</style>
