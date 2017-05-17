<template>
  <transition name='moveR'>
    <div v-show='showFlag' class='food' ref='food'>
      <div class="food-content">
        <div class='iamge-header'>
          <img :src="food.image">
          <div class='back' @click='hide'>
            <i class='icon icon-arrow_lift'></i>
          </div>
        </div>
        <div class="content">
          <h1 class='title'>{{food.name}}</h1>
          <div class='detail'>
            <span class='sell-count'>月售{{food.sellCount}}份</span>
            <span class='rating'>好评率{{food.rating}}%</span>
          </div>
          <div class='price'>
            <span class='now'>￥{{food.price}}</span><span class='old' v-show='food.oldPrice'>￥{{food.oldPrice}}</span>
          </div> 
        </div>
        <div class='cartcontroll-warpper'>
          <cartcontrol :food='food'></cartcontrol>
        </div>
        <transition name='fade'>
          <div @click='addFrist' class="buy" v-show='!food.count || food.count===0'>加入购物车</div>
        </transition>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';
  import bus from 'components/public.js';
  import BSscroll from 'better-scroll';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  export default {
    data () {
      return {
        showFlag: false
      };
    },
    props: {
      food: {
        type: Object
      }
    },
    methods: {
    // 食物详情显示
      show () {
        this.showFlag = true;
        // 控制内容滚动
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BSscroll(this.$refs.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          };
        });
      },
    // 食物详情隐藏
      hide () {
        this.showFlag = false;
      },
    // 加入购物车
      addFrist (event) {
        if (!event._constructed) {
          return;
        };
        bus.$emit('caradd', event.target);
        Vue.set(this.food, 'count', 1);
      }
    },
    components: {
      cartcontrol
    }
  };
  
</script>

<style lang='stylus' type="stylesheet/stylus">
  .food
    position: fixed
    top:0
    left:0
    bottom: 48px
    z-index:30
    width:100%
    background: #fff
    transform:translate3d(0,0,0)
    transition: all 0.5s linear
    &.moveR-enter-active
      transition: all 0.5s
    &.moveR-enter, &.moveR-leave-active
      transform:translate3d(100%,0,0)
    .iamge-header
      position: relative
      width:100%
      height:0
      padding-top:100%
      img
        position: absolute
        top:0
        left:0
        width:100%
        height:100%
      .back
        position: absolute
        top:10px
        left:0
        .icon-arrow_lift
          display:block
          padding: 10px
          font-size: 20px
          color: #fff
    .content
      padding: 18px
      .title
        line-height: 14px
        margin-bottom:8px
        font-size: 14px
        font-weight: 700
        color: rgb(7,17,27)
      .detail
        margin-bottom:18px
        line-height: 10px
        font-size: 0
        height: 10px
        .sell-count,.rating
          font-size: 10px
          color: rgb(147,153,159)
        .sell-count
          margin-right: 12px
      .price
            font-weight: 700
            line-height: 24px
            .now
              margin-right: 14px
              font-size: 14px
              color:rgb(240,20,20)
            .old
              text-decoration: line-through
              font-size: 10px
              color:rgb(147,153,159)
    .cartcontroll-warpper
      position: absolute
      right: 12px
      bottom: 12px
    .buy
      position:absolute
      right:18px
      bottom:18px
      z-index:10
      height:24px
      line-height:24px
      padding: 0 12px
      box-sizing:border-box
      font-size:10px
      border-radius:12px
      color:#fff
      background:rgb(0,160,220)
      transform: translate3d(0,0,0)
</style>
