<template>
  <transition name="fade">
    <div class="food" v-show="show" ref="food">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
          <div class="back">
            <i class="icon-arrow_lift" @click="hideDetail"></i>
          </div>
        </div>
        <div class="content">
          <div class="title">{{food.name}}</div>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}</span>
            <span class="rating">好评率{{food.price}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food"></cartcontrol>
          </div>
          <transition name="buy">
            <div @click="addFirst" class="buy" v-show="!food.count||food.count===0">加入购物车</div>
          </transition>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingselect
            :selectType.sync="selectType"
            :onlyContent.sync="onlyContent"
            :desc="desc"
            :ratings="food.ratings"
          ></ratingselect>
          <div class="ratings-wrapper">
            <ul v-show="food.ratings&&food.ratings.length">
              <li class="ratings-item border-1px" v-for="rating in food.ratings"
                  v-show="ratingShow(rating)">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" width="12" height="12" class="avatar">
                </div>
                <div class="time">{{rating.rateTime | dateFormat('YYYY-MM-DD HH:mm') }}</div>
                <p class="text">
                  <span
                    :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings||!food.ratings.length">暂无评价</div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'
  import BScroll from 'better-scroll'
  import moment from 'moment'
  import split from 'components/split/split.vue'
  import ratingselect from 'components/ratingselect/ratingselect.vue'
  import cartcontrol from 'components/cartcontrol/cartcontrol.vue'

  const ALL = 2

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        show: false,
        selectType: ALL,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    watch: {
      selectType () {
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      onlyContent () {
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      }
    },
    methods: {
      showDetail () {
        this.show = true
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      },
      hideDetail () {
        this.show = false
      },
      addFirst (event) {
        if (!event._constructed) {
          return
        }
        Vue.set(this.food, 'count', 1)
        this.$parent.$refs.shopcart.drop()
      },
      ratingShow ({rateType, text}) {
        if (!text && this.onlyContent) {
          return false
        }
        if (this.selectType === ALL) {
          return true
        } else {
          return rateType === this.selectType
        }
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    },
    filters: {
      dateFormat (time, fmt) {
        return moment(time).format(fmt)
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .food
    position: fixed
    top: 0
    right: 0
    bottom: 48px
    left: 0
    z-index: 0
    background: #fff
    transition: all 0.3s
    transform: translate3d(0, 0, 0)
    &.fade-enter-active, &.fade-leave-active
      transform: translate3d(100%, 0, 0)
    &.fade-enter-to
      transform: translate3d(0, 0, 0)
    .image-header
      position: relative
      width: 100%
      padding-top: 100%
      img
        position: absolute
        top: 0
        left: 0
        width: 100%
        height: 100%
      .back
        position: absolute
        top: 10px
        left: 0
        .icon-arrow_lift
          display: block
          padding: 10px
          font-size: 20px
          color: #fff
    .content
      position: relative
      padding: 18px
      .title
        line-height: 14px
        margin-bottom: 8px
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
      .detail
        margin-bottom: 18px
        line-height: 10px
        font-size: 0
        .sell-count, .rating
          font-size: 10px
          color: rgb(147, 153, 159)
        .sell-count
          margin-right: 12px
      .price
        font-weight: 700
        line-height: 24px
        .now
          margin-right: 8px
          font-size: 14px
          color: rgb(240, 20, 20)
        .old
          text-decoration: line-through
          font-size: 10px
          color: rgb(147, 153, 159)
      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        z-index: 10
        height: 12px
        line-height: 14px
        padding: 6px 12px
        box-size: box-sizing
        border-radius: 12px
        font-size: 10px
        color: #fff
        background: rgb(0, 160, 220)
        transition: all 0.3s
        &.buy-enter-active, &.buy-leave-to
          opacity: 0
        &.buy-enter-to
          opacity: 1
    .info
      padding: 18px
      .title
        font-size: 14px
        line-height: 14px
        margin-bottom: 6px
        color: rgb(7, 17, 27)
      .text
        font-size: 12px
        line-height: 24px
        font-weight: 200
        color: rgb(77, 85, 93)
    .rating
      padding-top: 18px
      .title
        font-size: 14px
        line-height: 14px
        margin-left: 18px
        color: rgb(7, 17, 27)
      .ratings-wrapper
        padding: 0 18px
        .ratings-item
          position: relative
          padding: 16px 0
          border-1px(rgba(7, 17, 27, 0.1))
          .user
            position: absolute
            right: 0
            top: 16px
            .name
              display: inline-block
              margin-right: 6px
              vertical-align: top
              font-size: 10px
              color: rgb(147, 153, 159)
            .avatar
              border-radius: 50%
          .time
            margin-bottom: 6px
            line-height: 12px
            font-size: 10px
            color: rgb(147, 153, 159)
          .text
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
            .icon-thumb_up, .icon-thumb_down
              margin-right: 4px
              line-height: 16px
              font-size: 12px
            .icon-thumb_up
              color: rgb(0, 160, 220)
            .icon-thumb_down
              color: rgb(147, 153, 159)
        .no-rating
          padding: 16px 0
          font-size: 12px
          color: rgb(147, 153, 159)
</style>
