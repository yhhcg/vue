<template>
    <scroll class="listview"
            :data="data"
            ref="listview"
            :listenScroll="listenScroll"
            @scroll="scroll"
    >
      <ul>
        <li v-for="group in data" class="list-group" ref="listGroup">
          <h2 class="list-group-title">{{group.title}}</h2>
          <ul>
            <li v-for="item in group.items" class="list-group-item">
              <img class="avatar" v-lazy="item.avatar">
              <span class="name">{{item.name}}</span>
            </li>
          </ul>
        </li>
      </ul>
      <div class="list-shortcut" @touchstart="onShortcutTouchStart" @touchmove.stop.prevent="onShortcutTouchMove">
        <ul>
          <li class="item" v-for="(item,index) in shortCutList" :class="{current: currentPageIndex === index}" :data-index="index">{{item}}</li>
        </ul>
      </div>
    </scroll>
</template>

<script>
    import Scroll from 'base/scroll/scroll'
    import {getData} from 'common/js/dom'

    const ANCHOR_HEIGHT = 18

    export default {
      props: {
        data: {
          type: Array,
          default: []
        }
      },
      data() {
        return {
          scrollY: 0
        }
      },
      created() {
        this.touch = {}
        this.listenScroll = true
        this.currentPageIndex = 0 // 当前list-group-item的索引
        this.listHeight = [] // 所有list-group-item的高度
      },
      computed: {
        shortCutList() {
          return this.data.map((group) => {
            return group.title.substr(0, 1)
          })
        }
      },
      methods: {
        onShortcutTouchStart(e) {
          let anchorIndex = getData(e.target, 'index')  // shortCutList数组索引
          this.touch.y1 = e.touches[0].pageY
          this.touch.anchorIndex = anchorIndex
          this._scrollTo(anchorIndex)
        },
        onShortcutTouchMove(e) {
          this.touch.y2 = e.touches[0].pageY
          let delta = (this.touch.y2 - this.touch.y1) / ANCHOR_HEIGHT | 0
          let anchorIndex = parseInt(this.touch.anchorIndex) + delta
          this._scrollTo(anchorIndex)
        },
        scroll(pos) {
          console.log(pos)
          this.scrollY = pos.y
        },
        _calculateHeight() {
          this.listHeight = []
          let list = this.$refs.listGroup
          let height = 0
          this.listHeight.push(height)
          for (let i = 0; i < list.length; i++) {
            height += list[i].clientHeight
            this.listHeight.push(height)
          }
        },
        _scrollTo(index) {
          this.$refs.listview.scrollToElement(this.$refs.listGroup[index])
        }
      },
      watch: {
        data() {
          setTimeout(() => {
            this._calculateHeight()
          }, 20)
        },
        scroll(newVal) {
          if (newVal > 0) {
            this.currentPageIndex = 0
            return false
          }
          for (let i = 0; i < this.listHeight.length; i++) {}
          if (newVal > -this.listHeight[this.listHeight.length - 2]) {
            this.currentPageIndex = this.listHeight.length - 2
            return false
          }
        }
      },
      components: {
        Scroll
      }
    }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .listview
    position: relative
    width: 100%
    height: 100%
    overflow: hidden
    background: $color-background
    .list-group
      padding-bottom: 30px
      .list-group-title
        height: 30px
        line-height: 30px
        padding-left: 20px
        font-size: $font-size-small
        color: $color-text-l
        background: $color-highlight-background
      .list-group-item
        display: flex
        align-items: center
        padding: 20px 0 0 30px
        .avatar
          width: 50px
          height: 50px
          border-radius: 50%
        .name
          margin-left: 20px
          color: $color-text-l
          font-size: $font-size-medium
    .list-shortcut
      position: absolute
      z-index: 30
      right: 0
      top: 50%
      transform: translateY(-50%)
      width: 20px
      padding: 20px 0
      border-radius: 10px
      text-align: center
      background: $color-background-d
      font-family: Helvetica
      .item
        padding: 3px
        line-height: 1
        color: $color-text-l
        font-size: $font-size-small
        &.current
          color: $color-theme
    .list-fixed
      position: absolute
      top: 0
      left: 0
      width: 100%
      .fixed-title
        height: 30px
        line-height: 30px
        padding-left: 20px
        font-size: $font-size-small
        color: $color-text-l
        background: $color-highlight-background
    .loading-container
      position: absolute
      width: 100%
      top: 50%
      transform: translateY(-50%)
</style>
