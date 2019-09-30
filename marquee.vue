<template>
  <div class="marquee-wrap">
    <!-- 外框，固定宽度 -->
    <div id="marquee-box" class="cl">
      <!-- 内部滚动框 -->
      <div id="marquee-text" @mouseenter="moveIn" @mouseleave="moveOut">{{ text }}</div>
    </div>
    <div id="marquee-node">{{ text }}</div>
  </div>
</template>
<script>
export default {
  name: 'MarqueeComponent',
  props: {
    lists: {
      type: Array,
      default: () => []
    }
  }, // 父组件传入数据， 数组形式
  data() {
    return {
      text: '', // 数组文字转化后的字符串
      marqueeTimer: null,
      distance: 0 // 位移距离
    }
  },
  // 更新的时候运动
  watch: {
    lists(val) {
      for (let i = 0; i < val.length; i++) {
        this.text += '   ' + val[i]
      }
      // 500毫秒后开始轮播
      setTimeout(() => {
        this.move()
      }, 500)
    }
  },
  destroyed() {
    if (this.marqueeTimer) {
      clearInterval(this.marqueeTimer)
    }
  },
  methods: {
    move() {
      let that = this
      that.$nextTick(() => {
        // 获取文字text 的计算后宽度 （由于overflow的存在，直接获取不到，需要独立的node计算）
        let width = document.getElementById('marquee-node').getBoundingClientRect().width
        let box = document.getElementById('marquee-box')
        // 设置位移
        that.marqueeTimer = setInterval(function() {
          that.distance = that.distance - 1
          // 如果位移超过文字宽度，则回到起点
          if (-that.distance >= width) {
            that.distance = 460
          }
          box.style.transform = 'translateX(' + that.distance + 'px)'
        }, 20)
      })
    },
    moveIn() {
      clearInterval(this.marqueeTimer)
    },
    moveOut() {
      this.move()
    }
  }
}
</script>
<style scoped>
/* 限制外框宽度，隐藏多余的部分 */
.marquee-wrap {
  overflow: hidden;
}
/* 移动框宽度设置 */
#marquee-box {
  width: 100%;
}
/* 文字一行显示 */
#marquee-box div {
  float: left;
}
/* 设置前后间隔 */
#marquee-text {
  min-width: 100%;
  height: 40px;
  margin: 0 16px 0 0;
  cursor: pointer;
}
/* 获取宽度的节点，隐藏掉 */
#marquee-node {
  position: absolute;
  z-index: -999;
  top: -999999px;
}
</style>
