<template>
  <div class='goods'>
    <div class='menu-wrapper' ref='menuWrapper'>
      <ul>
        <li v-for='(item,index) in goods' class='menu-item' :class="{'current':currentIndex===index}" @click='selectMenu(index,$event)'>
          <span class='text border-1px'>
            <span v-show="item.type>0" class="icon" :class='classMap[item.type]'></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class='foods-wrapper' ref='foodsWrapper'>
      <ul>
        <li v-for='item in goods' class='foos-list food-list-hook'>
          <h1 class='title'>{{item.name}}</h1>
          <ul>
            <li v-for='food in item.foods' class='food-item border-1px'>
              <div class='iconn'>
                <img :src="food.icon" width='57px' height='57px'>
              </div>
              <div class='content'>
                <h2 class='name'>{{food.name}}</h2>
                <p class='desc'>{{food.description}}</p>
                <div class='extra'>
                  <span class='count'>月售{{food.sellCount}}份</span>
                  <span class='rating'>好评率{{food.rating}}%</span>
                </div>
                <div class='price'>
                  <span class='now'>￥{{food.price}}</span><span class='old' v-show='food.oldPrice'>￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food='food'></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart ref:shopcart :select-foods='selectFoods' :deliveryPrice='seller.deliveryPrice' :minPrice='seller.minPrice'></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import BSscroll from 'better-scroll';
  import shopcart from 'components/shopCart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  const ERR_OK = 0;
  export default {
  // 接收父组件传递的seller信息
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0
      };
    },
    computed: {
    // 计算当前滚动到哪个索引区域
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          };
        };
        return 0;
      },
      selectFoods () {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            };
          });
        });
        return foods;
      }
    },
    created () {
    // 发起请求获取食品
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      this.$http.get('/api/goods').then((res) => {
        res = res.data;
        if (res.errno === ERR_OK) {
          this.goods = res.data;
        // 请求成功后调用函数
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          });
        };
      });
    },
    methods: {
      selectMenu (index, event) {
      // 取消原生浏览器二次点击事件
        if (!event._constructed) {
          return;
        };
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
      // 实现左右商品滑动联动效果
        this.foodsScroll.scrollToElement(el, 500);
      },
      // 利用滑动插件是两边商品能够滑动
      _initScroll () {
        this.menuScorll = new BSscroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BSscroll(this.$refs.foodsWrapper, {
          probeType: 3, // 派发事件
          click: true
        });
      // 获取当前食品滚动高度
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      // 获取每个区块的高度
      _calculateHeight () {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        };
      }
    },
    components: {
    // 声明组件
      shopcart,
      cartcontrol
    }
  };
</script>

<style lang='stylus' type="stylesheet/stylus">
  @import '../../common/stylus/mixin';
  .goods
    display: flex
    overflow: hidden
    position: absolute
    top: 174px
    bottom: 46px
    width:100%
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        line-height: 14px
        padding:0 12px
        &.current
          position:relative
          margin-top:-1px
          z-index:10
          background:#fff
          font-weight: 700
          .text
            border-none()
        .icon
          display:inline-block
          vertical-align:top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
           bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          text-align: center
          display: table-cell
          font-size: 12px
          width: 56px
          border-1px(rgba(7,17,27,0.1))
          vertical-align: middle
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 1px solid #d9dde1
        font-size: 12px
        color:rgb(147,153,159)
        background:#f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom:18px
        border-1px(rgba(7,17,27,0.1))
        &:last-chlid
          border-none()
          margin-bottom: 0
        .iconn
          flex:0 0 57px
          margin-right: 10px
        .content
          flex:1
          .name
            margin: 2px 0 8px 0
            height: 14px
            line-height: 14px
            font-size: 14px
            color:rgb(7,17,27)
          .desc, .extra
            line-height: 10px
            font-size: 10px;
            color:rgb(147,153,159)
          .desc
            margin-bottom: 8px
            line-height: 12px
          .extra
            & .count
              margin-right:12px
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
          .cartcontrol-wrapper
            position:absolute
            right:0
            bottom:0px
</style>
