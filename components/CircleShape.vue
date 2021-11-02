<template lang="pug">
  .circle-shape
    .circle-shape__box
      .circle-shape__image(:style="`transform: ${transform}`")
        img(src="/circle.png" alt="circle")
        .circle-inner
        .dot-wrap(
          v-for="(option, index) in options"
          :key="option.title + 'dot' + index"
          :style="`transform: rotate(${180 - option.angle}deg)`")
          .dot(:style="dotAnimation(index, animation) + `background: ${option.color};`")

    .option(
      v-for="(option, index) in refinedOptions"
      :key="option.title + index"
      :class="calcSide(option.angle)")
      .shape-outside
        img(src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mNk+M9QDwADhgGAWjR9awAAAABJRU5ErkJggg==")

      transition-group(name="point" tag="div" @before-enter="beforeEnter" @enter="enter" appear)
        .title(:style="calcTitle(option)" key="title")
          span(v-html="option.title")
        .point(v-for="(item, index) in option.items" :key="item + index" :data-index="index")
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
    animation: {
      type: String,
      default: null,
    },
  },
  computed: {
    refinedOptions() {
      return this.animation === null
        ? this.options
        : [{ ...this.options[+this.animation], angle: 60 }]
    },
    transform() {
      if (this.animation === null) return 'rotate(0deg)'
      return `rotate(${this.options[+this.animation].angle - 60}deg)`
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

    dotAnimation(index: number, animation: number) {
      if (animation === null) return ''
      return `
        opacity: ${+animation === index ? 1 : 0};
        transform: scale(${+animation === index ? 1 : 0});
      `
    },

    beforeEnter(el: any) {
      el.style.opacity = 0
    },
    enter: function (el: any) {
      const count = this.refinedOptions[0].items.length
      const delay = el.dataset.index * (500 / count)

      setTimeout(function () {
        el.style.opacity = 1
      }, delay)
    },
  },
})
</script>

<style lang="scss" scoped>
.circle-shape {
  position: relative;
  display: flex;
  justify-content: center;
  padding: 0 180px;
  max-height: 520px;
  width: 100%;

  @media screen and (max-width: 900px) {
    left: -40%;
    padding: 0;
    margin-right: 50px;
  }

  // -= CIRCLE =-
  .circle-shape__box {
    max-width: 520px;
    width: 100%;

    .circle-shape__image {
      position: relative;
      padding-top: 100%;
      transition: 0.5s;

      img {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
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

      .dot-wrap {
        position: absolute;
        right: 50%;
        top: calc(50% - 10px);
        width: calc(50% + 10px);
        transform-origin: 100% 10px;

        .dot {
          width: 20px;
          height: 20px;
          border-radius: 50%;
          transition: 0.5s;
        }
      }
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
      transition: all 0.6s;
      span {
        padding: 15px 15px;
      }
    }

    .point {
      font-size: 12px;
      line-height: 16px;
      transition: all 0.6s;

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
