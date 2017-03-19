<template>
  <div class='shopcart'>
    <div class="content" @click='toggleList'>
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'hightlight':totalCount>0}">
            <i class='icon icon-shopping_cart' :class="{'hightlight':totalCount>0}"></i>
          </div>
          <div class="num" v-show='totalCount>0'>{{totalCount}}</div>
        </div>
        <div class="price" :class="{'hightlight':totalPrice>0}">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right">
        <div class="pay" :class='payClass'>
          {{payDesc}}
        </div>
      </div>
    </div>
    <div class="ball-container">
      <transition v-for='ball in balls' name='drop' @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter" :key='ball.id'>
        <div class='ball' v-show='ball.show'>
        <div class="inner inner-hook"></div>
        </div>
      </transition>
    </div>
    <transition name='fold'>
      <div class="shopcart-list" v-show='listShow' >
        <div class="move">
            <div class="listHeader">
              <h1 class='title'>购物车</h1>
              <span class='empty'>清空</span>
            </div>
            <div class="listContent">
            <ul>
              <li class='food' v-for='food in selectFoods'>
                <span class='name'>{{food.name}}</span>
                <div class='price'>
                  <span>￥{{food.price*food.count}}</span>
                </div>
                <div class='cartControl-wrapper'>
                  <cartcontrol :food='food'></cartcontrol>
                </div>
              </li>
            </ul>
            </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import bus from 'components/public.js';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  export default {
    props: {
    // 接收父组件传递的数据
      selectFoods: {
        type: Array,
        default () {
          return [
            {
              price: 10,
              count: 3
            }
          ];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data () {
      return {
        balls: [
          {
            show: false,
            id: 1
          },
          {
            show: false,
            id: 2
          },
          {
            show: false,
            id: 3
          },
          {
            show: false,
            id: 4
          },
          {
            show: false,
            id: 5
          }
        ],
        dropBall: [],
        fold: false
      };
    },
    computed: {
    // 计算总价格
      totalPrice () {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
    // 计算商品数量
      totalCount () {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
    // 支付信息描述
      payDesc () {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差￥${diff}元起送`;
        } else {
          return '去结算';
        }
      },
    // 满多少元起送
      payClass () {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      },
    // 显示购买的商品信息
      listShow () {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        };
        let show = !this.fold;
        return show;
      }
    },
    created () {
      // 接收cartcontrl派发的信息
      bus.$on('caradd', (el) => {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBall.push(ball);
            return;
          };
        }
      });
    },
    methods: {
      // 小球动画
      beforeEnter (el) {
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect();
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22);
            el.style.display = '';
            el.style.webkitTransform = `translate3d(0,${y}px,0)`;
            el.style.transform = `translate3d(0,${y}px,0)`;
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
            inner.style.transform = `translate3d(${x}px,0,0)`;
          };
        };
      },
      enter (el) {
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight;
        this.$nextTick(() => {
          el.style.webkitTransform = 'translate3d(0, 0, 0)';
          el.style.transform = 'translate3d(0, 0, 0)';
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = 'translate3d(0, 0, 0)';
          inner.style.transform = 'translate3d(0, 0, 0)';
        });
      },
      afterEnter (el) {
        let ball = this.dropBall.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none';
        };
      },
    // 折叠购物车
      toggleList () {
        if (!this.totalCount) {
          return;
        };
        this.fold = !this.fold;
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang='stylus' type="stylesheet/stylus">
  .shopcart
    position: fixed
    left: 0
    bottom: 0
    z-index: 100
    width: 100%
    height: 48px
    .content
      display: flex
      background: #141d27
      color: rgba(255,255,255,0.4)
      font-size: 0
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          vertical-align: top
          border-radius: 50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            background: #2b343c
            text-align: center
            &.hightlight
              background:rgb(0,160,220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.hightlight
                color:#fff
          .num
            position: absolute
            top:0
            right:0
            width: 24px
            height: 16px
            line-height:16px
            text-align: center
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            color: #fff
            background: rgb(240,20,20)
            box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4)
        .price
          display: inline-block
          vertical-align: top
          line-height: 24px
          margin-top: 12px
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255,255,255,0.1)
          font-size: 16px
          font-weight: 700
          &.hightlight
            color:#fff
        .desc
          display: inline-block
          vertical-align: top
          margin: 12px 0 0 12px
          line-height: 24px
          font-size: 10px
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 12px
          font-weight: 700
          background: #2b333b
          &.not-enough
            background:#2b333b
          &.enough
            background:#00b43c
            color: #fff
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        transition: all 0.8s cubic-bezier(0.49,-0.29,0.75,0.41)
        .inner
          width: 16px
          height: 16px
          border-radius: 50%
          background: rgb(0,160,220)
          transition: all 0.8s linear
    .shopcart-list
      &.fold-enter-active, &.fold-leave-active
        transition:.5s
      &.fold-enter, &.fold-leave-active
       .move
          transform: translate3d(0,100%, 0)
      .move
        display:inline-block
        position: absolute
        top:0
        left:0
        z-index:-1
        width:100%
        transition: all 0.5s linear
        transform: translate3d(0,-100%, 0)
      

</style>
