<template>
  <div id="hy-swiper">
    <!-- 内容容器 -->
    <div class="swiper" @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
      <!-- 内容插槽 -->
      <slot></slot>
    </div>
    <slot name="indicator"></slot>
    <!-- 锚点容器 -->
    <div class="indicator">
      <slot name="indicator" v-if="showIndicator && slideCount > 1">
        <div v-for="(item,index) in slideCount" class="indi-item" :class="{active:index===currentIndex-1}" :key="index"></div>
      </slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Swiper',
  props: {
    // 轮播图定时器调用的时间
    interval: {
      type: Number,
      default: 3000
    },
    // 动画的执行时间
    animDuration: {
      type: Number,
      default: 300
    },
    // 手指滑动偏移量为多少可切换图片
    moveRatio: {
      type: Number,
      default: 0.25
    },
    // 是否显示锚点
    showIndicator: {
      type: Boolean,
      default: true
    }
  },
  data: function() {
    return {
      slideCount: 0,  //元素个数 
      currentIndex: 1,  //当前显示图片的索引
      totalWidth: 0,  //当前swiper宽度
      swiperStyle: {},  //当前swiper样式
      scrolling: false  //判断是否正在滚动
    }
  },
  mounted: function() {
    setTimeout(() => {
      // 操作DOM 左右添加slide
      this.handleDom()

      this.starTimer()
      
    }, 300);
  },
  methods: {
    // 操作DOM 前后添加slide 实现左右滑动无缝衔接 
    handleDom: function() {
      // 获取父级元素 获取子元素
      let swiperEl = document.querySelector('.swiper')
      let slidesEls = document.querySelectorAll('.slide')
      // 先保存数据
      this.slideCount = slidesEls.length
      // 判断是否有数据
      if(this.slideCount > 1){
        let cloneFisrt = slidesEls[0].cloneNode(true)
        let cloneLast = slidesEls[slidesEls.length-1].cloneNode(true)
        // 将复制的最后一个数据放置在第一
        swiperEl.insertBefore(cloneLast,slidesEls[0])
        // 将复制的第一个数据放置在最后
        swiperEl.appendChild(cloneFisrt)
        this.swiperStyle = swiperEl.style
        this.totalWidth = swiperEl.offsetWidth
        // 设置图片显示为第一页
        this.setTransform(-this.totalWidth)
      }
    },
    // 设置轮播图定时器
    starTimer: function() {
      this.playTimer = window.setInterval(() => {
        this.currentIndex++;
        // console.log(this.totalWidth)
        this.scrollContent(-this.currentIndex * this.totalWidth)
      }, this.interval);
    },

    // 停止定时器
    stopTimer: function() {
      window.clearInterval(this.playTimer)
    },

    // 设置图片位移
    setTransform: function(position) {
      this.swiperStyle.transform = `translate3d(${position}px,0,0)`
    },

    // 设置图片的滚动
    scrollContent: function(currentPosition) {
      // 进入图片滚动函数
      this.scrolling = true;
      // 设置动画时间及调用位移函数
      this.swiperStyle.transition = 'transform '+ this.animDuration +'ms';
      this.setTransform(currentPosition);

      // 判断滚动位置 使轮播图正确滚动
      this.checkPosition()
      // 一张图片的滚动结束
      this.scrolling = false
    },

    // 判断滚动位置 调整滚动索引
    checkPosition: function() {
      setTimeout(() => {
        // 先将动画关闭
        this.swiperStyle.transition = '0ms'
        // 当索引值大于等于slide元素的个数+1时 此时显示的是克隆的第一张页数 应使图片快速切换为真正的第一张 
        // 即将当前显示的图片的索引变更为1 并调用图片位移函数
        if (this.currentIndex >= this.slideCount + 1) {
          this.currentIndex = 1
          this.setTransform(-this.currentIndex * this.totalWidth)
        } else if (this.currentIndex <=0) {
          this.currentIndex = this.slideCount
          this.setTransform(-this.currentIndex * this.totalWidth)
        }
      }, this.animDuration);
    },

    // 手指开始接触屏幕
    touchstart: function(e) {
      // console.log(e)
      // 判断图片是否是在滚动
      if (this.scrolling) return
      // 关闭轮播图滚动动画
      this.stopTimer()
      // 记录手机接触屏幕时的初始位置
      this.startX = e.touches[0].pageX

    },

    // 手指滑动图片
    touchmove: function(e) {
      this.moveX = e.touches[0].pageX
      // 滑动的距离
      this.distance = this.moveX - this.startX
      // 当前的位置
      let currentPosition = -this.currentIndex * this.totalWidth
      let moveDistance = currentPosition + this.distance
      // 正数为向右滑动 负数为向左滑动
      this.setTransform(moveDistance)
    },

    // 手指滑动完成
    touchend: function() {
      let currentMove = Math.abs(this.distance)
      if (this.distance===0){
        return
      } else if (this.distance > 0 && currentMove > this.totalWidth * this.moveRatio) {
        this.currentIndex--
      } else if (this.distance < 0 && currentMove > this.totalWidth * this.moveRatio) {
        this.currentIndex++
      }
      // 移动到正确位置
      this.scrollContent( -this.currentIndex * this.totalWidth)
      // 开启定时器
      this.starTimer()
    },

    // 控制
    // 上下

    previous: function() {
      this.changeItem(-1)
    },
    
    next: function() {
      this.changeItem(1)
    },

    changeItem: function(num) {
      // 需先关闭定时器
      this.stopTimer()
      // 索引变更
      this.currentIndex += num
      // 调用图片轮播动画
      this.scrollContent(-this.currentIndex * this.totalWidth)

      // 启用定时器
      this.starTimer()
    }
  }
}
</script>

<style>
  #hy-swiper {
    overflow: hidden;
    position: relative;
  }
  .swiper {
    display: flex;
  }
  .indicator {
    position: absolute;
    bottom: 8px;
    display: flex;
    width: 100%;
    justify-content: center;
  }
  .indi-item {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    margin-left: 5px;
    background-color: #fff;
  }
  .indi-item.active {
    background-color: red;
  }
</style>