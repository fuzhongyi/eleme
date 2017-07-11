<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect
        :selectType.sync="selectType"
        :onlyContent.sync="onlyContent"
        :ratings="ratings"
      ></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-for="rating in ratings" class="rating-item" v-show="ratingShow(rating)">
            <div class="avatar">
              <img :src="rating.avatar" width="28" height="28">
            </div>
            <div class="content">
              <div class="title">
                <span class="name">{{rating.username}}</span>
                <span class="time">{{rating.rateTime | dateFormat('YYYY-MM-DD HH:mm')}}</span>
              </div>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <i class="icon" :class="{
                      'icon-thumb_up':rating.rateType===0,
                      'icon-thumb_down':rating.rateType===1
                    }">
              </i>
              <div v-for="item in rating.recommend" class="recommend-item">
                {{item}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import moment from 'moment'
  import star from 'components/star/star.vue'
  import split from 'components/split/split.vue'
  import ratingselect from 'components/ratingselect/ratingselect.vue'

  const ALL = 2
  const ERR_OK = 0

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        ratings: [],
        show: false,
        selectType: ALL,
        onlyContent: false
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
      star,
      split,
      ratingselect
    },
    created () {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body
        if (response.errNo === ERR_OK) {
          this.ratings = response.data
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratings, {
              click: true
            })
          })
        }
      })
    },
    filters: {
      dateFormat (time, fmt) {
        return moment(time).format(fmt)
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .ratings
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .overview
      display: flex
      padding: 18px 0
      .overview-left
        flex: 0 0 137px
        padding: 6px 0
        width: 137px
        border-right: 1px solid rgba(7, 17, 27, 0.1)
        text-align: center
        @media only screen and (max-width: 320px)
          flex: 0 0 120px
          width: 120px
        .score
          margin-bottom: 6px
          font-size: 24px
          line-height: 28px
          color: rgb(255, 153, 0)
        .title
          margin-bottom: 8px
          font-size: 12px
          line-height: 12px
          color: rgb(7, 17, 27)
        .rank
          font-size: 10px
          line-height: 10px
          color: rgb(147, 153, 159)
      .overview-right
        padding: 6px 0 0 24px
        @media only screen and (max-width: 360px)
          padding-left: 12px
        @media only screen and (max-width: 320px)
          padding-left: 6px
        .score-wrapper, .delivery-wrapper
          line-height: 18px
          .title
            display: inline-block
            vertical-align: top
            margin-bottom: 12px
            font-size: 12px
            color: rgb(7, 17, 27)
          .star
            display: inline-block
            vertical-align: top
            margin: 0 12px
            @media only screen and (max-width: 360px)
              margin: 0 6px
          .score
            display: inline-block
            vertical-align: top
            font-size: 12px
            color: rgb(255, 153, 0)
          .time
            display: inline-block
            vertical-align: top
            margin-left: 12px
            font-size: 12px
            color: rgb(147, 153, 159)
            @media only screen and (max-width: 360px)
              margin-left: 6px
    .rating-wrapper
      padding: 0 18px
      .rating-item
        display: flex
        padding: 18px 0
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .avatar
          flex: 0 0 52px
          width: 52px
          img
            border-radius: 50%
        .content
          flex: 1
          .title
            font-size: 10px
            line-height: 12px
            .name
              color: rgb(7, 17, 27)
            .time
              float: right
              font-weight: 200
              color: rgb(147, 153, 159)
        .star-wrapper
          margin: 4px 0 6px 0
          line-height: 12px
          .star
            display: inline-block
          .delivery
            display: inline-block
            font-size: 10px
            font-weight: 200
            color: rgb(147, 153, 159)
        .text
          font-size: 12px
          line-height: 18px
          color: rgb(7, 17, 27)
          margin-bottom: 8px
        .icon
          display: inline-block
          position: relative
          top: 2px
          font-size: 12px
          line-height: 16px
          &.icon-thumb_up
            color: rgb(0, 160, 220)
          &.icon-thumb_down
            color: rgb(183, 187, 191)
        .recommend
          display: inline-block
        .recommend-item
          display: inline-block
          padding: 0 6px
          margin: 4px 0 0 8px
          font-size: 9px
          line-height: 16px
          border: 1px solid rgba(7, 17, 27, 0.1)
          border-radius: 2px
          background-color: rgb(255, 255, 255)
          color: rgb(147, 153, 159)
</style>
