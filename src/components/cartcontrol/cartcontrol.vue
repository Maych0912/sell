<template>
  <div class="cartcontrol">
    <transition name='fade'>
      <div class="cart-decrease " v-show='food.count>0' @click='decCart'>
      <i class='inner icon icon-remove_circle_outline'></i>
      </div>
    </transition>
    <div class="cart-count" v-show='food.count>0'>{{food.count}}</div>
    <div class="cart-add icon icon-add_circle" @click='addCart'></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';
  import bus from 'components/public.js';
  export default {
    props: {
    // 接收父组件传递的food
      food: {
        type: Object
      }
    },
    methods: {
    // 添加商品数量
      addCart (event) {
        if (!event._constructed) {
          return;
        };
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        };
        bus.$emit('caradd', event.target);
      },
    // 减少商品数量
      decCart (event) {
        if (!event._constructed) {
          return;
        };
        if (this.food.count) {
          this.food.count--;
        };
      }
    }
  };
</script>

<style lang='stylus' type="stylesheet/stylus">
   .cartcontrol
    font-size: 0
    .cart-decrease
      transform:translate3d(0,0,0)
      display:inline-block
      padding:6px
      &.fade-enter-active
        transition: all 0.5s linear
      &.fade-enter, &.fade-leave-active
        transition: all 0.5s linear
        transform:translateX(24px) rotateZ(90deg)
      .inner
        font-size: 24px
        line-height:24px
        color: rgb(0,160,220)
    .cart-count
      display: inline-block
      vertical-align: top
      width:12px
      padding-top:6px
      line-height: 24px
      text-align: center
      font-size:10px
      color:rgb(147,153,159)
    .cart-add
      display: inline-block
      padding:6px
      font-size: 24px
      line-height:24px
      color: rgb(0,160,220)

</style>
