<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="info">
        <div class="info-up">
          <div class="up-left">
            <h1 class="title">{{seller.name}}</h1>
            <star :size="36" :score="seller.score"></star>
            <div class="rating-count">({{seller.ratingCount}})</div>
            <div class="sell-count">月售{{seller.sellCount}}单</div>
          </div>
          <div class="up-right" @click="toggleCollect">
            <transition name="fav">
              <i class="icon-favorite" :class="{'on':isCollected}" ref="collect"></i>
            </transition>
            <span class="desc">{{isCollected ? '已收藏' : '收藏'}}</span>
          </div>
        </div>
        <div class="info-down">
          <div class="content">
            <h2 class="title">起送价</h2>
            <span class="num">{{seller.minPrice}}</span>
            <span class="unit">元</span>
          </div>
          <div class="content">
            <h2 class="title">商家配送</h2>
            <span class="num">{{seller.deliveryPrice}}</span>
            <span class="unit">元</span>
          </div>
          <div class="content">
            <h2 class="title">平均配送时间</h2>
            <span class="num">{{seller.deliveryTime}}</span>
            <span class="unit">分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <div class="bulletin-supports">
        <h1 class="title">公告与活动</h1>
        <p class="bulletin">{{seller.bulletin}}</p>
        <ul class="supports-wrapper">
          <li v-for="support in seller.supports" class="supports-item">
            <span class="icon" :class="classMap[support.type]"></span>
            <span class="description">{{support.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pics-wrapper" ref="pics">
          <ul class="pics-list" ref="picsList">
            <li class="pics-item" v-for="item in seller.pics">
              <img :src="item" width="110" height="90">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="infos">
        <h1 class="title">商家信息</h1>
        <ul class="infos-wrapper">
          <li v-for="item in seller.infos" class="infos-item">
            <span class="description">{{item}}</span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from 'components/star/star.vue'
  import split from 'components/split/split.vue'
  import BScroll from 'better-scroll'

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        isCollected: false,
        classMap: ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      }
    },
    methods: {
      toggleCollect () {
        let val = !this.isCollected
        let collectElem = this.$refs.collect
        if (val) {
          collectElem.style.animation = 'collect .8s'
        } else {
          collectElem.style.animation = 'none'
        }
        localStorage.isCollected = val
        this.isCollected = val
      }
    },
    created () {
      localStorage.isCollected === 'true' ? this.isCollected = localStorage.isCollected : ''
      this.$nextTick(() => {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        })
        if (this.seller.pics) {
          let picWidth = 110
          let marginRight = 6
          let width = (picWidth + marginRight) * this.seller.pics.length - 6
          this.$refs.picsList.style.width = width + 'px'
        }
        this.picScorll = new BScroll(this.$refs.pics, {
          scrollX: true,
          eventPassthrough: 'vertical'
        })
      })
    },
    components: {
      star,
      split
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .seller
    position: absolute
    top: 174px
    right: 0
    bottom: 0
    left: 0
    overflow: hidden
    h1.title
      font-size: 14px
      line-height: 14px
      color: rgb(7, 17, 27)
    .info
      padding: 18px
      .info-up
        display: flex
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        .up-left
          flex: 1
          .title
            margin-bottom: 8px
          .star, .rating-count, .sell-count
            display: inline-block
            vertical-align: top
            font-size: 10px
            line-height: 18px
            color: rgb(77, 85, 93)
          .rating-count
            margin: 0 12px 0 8px
        .up-right
          flex: 0 0 50px
          width: 50px
          .icon-favorite
            display: block
            font-size: 24px
            line-height: 24px
            text-align: center
            color: rgb(183, 187, 191)
            &.on
              color: rgb(240, 20, 20)
          .desc
            display: block
            margin-top: 4px
            font-size: 10px
            line-height: 10px
            text-align: center
            color: rgb(77, 85, 93)
      .info-down
        padding-top: 18px
        display: flex
        .content
          flex: 1
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          font-size: 0
          text-align: center
          &:last-child
            border-right: none
          .title
            display: block
            margin-bottom: 4px
            font-size: 10px
            line-height: 10px
            color: rgb(147, 153, 159)
          .num, .unit
            font-weight: 200
            line-height: 24px
            color: rgb(7, 17, 27)
          .num
            font-size: 24px
          .unit
            font-size: 10px
    .bulletin-supports
      padding: 18px
      .bulletin
        padding: 8px 12px 16px 12px
        font-size: 12px
        font-weight: 200
        line-height: 24px
        color: rgb(240, 20, 20)
      .supports-wrapper
        .supports-item
          padding: 16px 12px
          font-size: 0
          border-top: 1px solid rgba(7, 17, 27, 0.1)
          .icon
            display: inline-block
            width: 16px
            height: 16px
            vertical-align: top
            margin-right: 6px
            background-size: 16px 16px
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
          &:last-child
            padding-bottom: 0px
          .description
            font-size: 12px
            font-weight: 200
            line-height: 16px
            color: rgb(7, 17, 27)
    .pics
      padding: 18px
      .title
        margin-bottom: 12px
      .pics-wrapper
        width: 100%
        overflow: hidden
        white-space: nowrap
        .pics-list
          font-size: 0
          .pics-item
            display: inline-block
            margin-right: 6px
            width: 110px
            height: 90px
            &:last-child
              margin-right: 0
    .infos
      padding: 18px
      .title
        margin-bottom: 12px
      .infos-wrapper
        .infos-item
          padding: 16px 12px
          font-size: 0
          border-top: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            padding-bottom: 0px
          .description
            font-size: 12px
            font-weight: 200
            line-height: 16px
            color: rgb(7, 17, 27)

  @keyframes collect
    0%
      transform: scale(1)
    25%
      transform: scale(1.2)
    50%
      transform: scale(0.5)
    75%
      transform: scale(1.5)
    100%
      transform: scale(1)
</style>
