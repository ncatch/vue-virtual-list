<template>
  <div class="yz-virtual-list">
    <div
      v-for="(item, index) in resultList"
      :id="getDivId(index)"
      :key="item.id"
      :class="itemClass"
      :style="'top:' + unitHeight * (index + start) + 'px'"
    >
      <slot :row="item" />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'

@Component
export default class virtualList extends Vue {
  @Prop() data: any
  @Prop() itemClass: String|undefined

  unitHeight = 0;
  page = 15
  start = 0
  end = 15
  observer: IntersectionObserver|null = null

  getDivId (index: number) {
    if (index === 0) {
      return 'top'
    } else if (index === 14) {
      return 'bottom'
    }
    return ''
  }

  get resultList () {
    return this.data.slice(this.start, this.end)
  }

  watchEl () {
    if (this.observer) {
      const topEl = document.getElementById('top')
      topEl && this.observer.observe(topEl)

      const bottomEl = document.getElementById('bottom')
      bottomEl && this.observer.observe(bottomEl)
    }
  }

  mounted () {
    const self = this

    // 获取单个元素的高度
    this.$el.firstElementChild && (this.unitHeight = this.$el.firstElementChild.clientHeight)

    // 头部元素和尾部元素的可见状态变化的处理逻辑
    this.observer = new IntersectionObserver(
      (entries, observer) => {
        entries.forEach((entry, index) => {
          const { start, end, page } = this
          const listLength = this.data.length
          // 向下滚动，刷新数据
          if (entry.isIntersecting && entry.target.id === 'bottom') {
            const maxStartIndex = listLength - 1 - page // 当前头部的索引
            const maxEndIndex = listLength - 1 // 当前尾部的索引
            const newEnd = (end + 10) <= maxEndIndex ? end + 10 : maxEndIndex // 下一轮增加尾部
            const newStart = (end - 5) <= maxStartIndex ? end - 5 : maxStartIndex // 在上一轮的基础上计算头部
            this.start = newStart
            this.end = newEnd
          }
          // 向上滚动，刷新数据
          if (entry.isIntersecting && entry.target.id === 'top') {
            const newEnd = end === page ? page : (end - 10 > page ? end - 10 : page) // 向上滚动尾部元素索引不得小于15
            let newStart = start === 0 ? 0 : (start - 10 > 0 ? start - 10 : 0) // 头部元素索引最小值为0
            this.start = newStart
            this.end = newEnd
          }
          self.watchEl()
        })
      },
      {
        threshold: [0, 0.5],
        root: this.$el,
        rootMargin: '10px 10px 30px 20px'
      }
    )

    self.watchEl()
  }
}
</script>

<style lang="less">
.yz-virtual-list {
  height: 400px;
  overflow: auto;
  position: relative;

  > div {
    position: absolute;
  }
}
</style>
