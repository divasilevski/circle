<template lang="pug">
  .circle-shape
    .circle-shape__image
      img(src="/circle.png" alt="circle" :style="`transform: ${rotate}`")
      .circle-inner
      .dot(
        v-for="(option, index) in options"
        :key="option.title + 'dot' + index"
        :style="calcDot(option)")

    .option(
      v-for="(option, index) in options"
      :key="option.title + index"
      :class="calcSide(option.angle)")
      .shape-outside
        img(src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mNk+M9QDwADhgGAWjR9awAAAABJRU5ErkJggg==")
      .title(:style="calcTitle(option)")
        span(v-html="option.title")
      .point(v-for="(item, index) in option.items" :key="item + index")
        .text
          span(v-html="item")
          .bullet(:style="`background: ${option.color}`")

</template>

<script lang="ts">
import Vue from 'vue'

function getRad(deg: number): number {
  return (deg * Math.PI) / 180
}

export interface CirclePoint {
  title: string
  items: string[]
  angle: number
  color: string
}

export default Vue.extend({
  props: {
    options: {
      type: Array as () => CirclePoint[],
      default: [],
    },
    rotate: {
      type: String,
      default: 'rotate(0deg)',
    },
  },
  methods: {
    calcSide(angle: number) {
      return Math.cos(getRad(angle)) < 0 ? 'left' : 'right'
    },
    calcTitle(option: CirclePoint) {
      const sin = Math.sin(getRad(option.angle))
      const cos = Math.sin(getRad(option.angle))
      const top = 260 - 260 * sin
      const shift = cos > 0 ? -40 : -20
      return `margin-top: ${top + shift}px; color: ${option.color};`
    },
    calcDot(option: CirclePoint) {
      const cos = Math.cos(getRad(option.angle))
      const sin = Math.sin(getRad(option.angle))
      const left = 260 + 260 * cos - 10
      const bottom = 260 + 260 * sin - 10
      return `left: ${left}px; bottom: ${bottom}px; background: ${option.color};`
    },
  },
})
</script>

<style lang="scss" scoped>
.circle-shape {
  position: relative;
  display: flex;
  justify-content: center;
  padding: 0 200px;
  max-height: 520px;
  width: 100%;
  // height: 100%; ?????

  .circle-shape__image {
    position: relative;
    max-width: 520px;
    width: 100%;
    padding-top: 0%;

    img {
      width: 100%;
      height: 100%;
      transition: 1s;
    }

    .circle-inner {
      position: absolute;
      bottom: 5px;
      right: 5px;
      left: 5px;
      top: 5px;
      background: white;
      border-radius: 100%;
    }

    .dot {
      position: absolute;
      width: 20px;
      height: 20px;
      border-radius: 50%;
    }
  }

  // -= OPTION =-
  .option {
    position: absolute;
    bottom: 0;
    right: 0;
    left: 0;
    top: 0;

    .shape-outside {
      display: inline-block;
      height: 100%;

      img {
        visibility: hidden;
        height: 100%;
      }
    }

    .title {
      font-size: 20px;
      line-height: 27px;
      span {
        padding: 15px 15px;
      }
    }

    .point {
      font-size: 12px;
      line-height: 16px;

      .text {
        position: relative;
        display: inline-block;
        max-width: 160px;
        margin: 5px 10px;
        padding-right: 16px;

        .bullet {
          position: absolute;
          right: 0;
          top: 6px;
          width: 6px;
          height: 6px;
          border-radius: 50%;
        }
      }
    }

    &.left {
      right: 50%;
      text-align: right;
      .shape-outside {
        float: right;
        shape-outside: circle(50% at 100%);
      }
    }

    &.right {
      left: 50%;
      .shape-outside {
        float: left;
        shape-outside: circle(50% at 0%);
      }
      .text {
        padding-left: 16px;
        .bullet {
          left: 0;
        }
      }
    }
  }
}
</style>
