<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease"
           v-show="food.count>0" @click.stop.prevent="decreaseCart">
        <i class="inner icon-remove_circle_outline"></i>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart($event)"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart (event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        this.$store.state.ballElem = event.target
        // 效果优化
        this.$nextTick(() => {
          try {
            this.$parent.$refs.shopcart.drop()
          } catch (e) {
            this.$parent.$parent.$refs.shopcart.drop()
          }
        })
      },
      decreaseCart () {
        if (!event._constructed) {
          return
        }
        if (this.food.count) {
          this.food.count--
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease, .cart-add
      display: inline-block
      padding: 6px
      transition: all .3s linear
      .inner
        display: inline-block
        font-size: 24px
        line-height: 24px
        color: rgb(0, 160, 220)
        transform: rotate(0deg)
        transition: all .3s linear
      &.move-enter-active, &.move-leave-to
        opacity: 0
        transform: translate3D(24px, 0, 0)
        .inner
          transform: rotate(180deg)
      &.move-enter-to
        opacity: 1
        transform: translate3D(0, 0, 0)
        .inner
          transform: rotate(0deg)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      text-align: center
      font-size: 10px
      color: rgb(147, 153, 159)
    .cart-add
      display: inline-block
      font-size: 24px
      line-height: 24px
      color: rgb(0, 160, 220)
</style>
