<template lang="pug">
  .circle(data-circle)
    img(src="/circle.png" alt="circle" :style="`transform: ${rotate}`")
    .circle-inner
    .points(data-points)
</template>

<script lang="ts">
// types
export interface CirclePoint {
  title: string
  items: string[]
  angle: number
  color: string
  clockwise?: boolean
}

//  function helpers
function polarToCartesian({ angle, radius }: any) {
  const cos = Math.cos(getRad(angle))
  const sin = Math.sin(getRad(angle))
  const x = radius * cos
  const y = radius * sin
  return coordsShift({ x, y, radius })
}

function coordsShift({ x, y, radius }: any) {
  return {
    x: x + radius,
    y: y + radius,
  }
}

function coordsToPercents({ x, y }: any, radius: number) {
  const side = radius * 2
  return { x: (x * 100) / side, y: (y * 100) / side }
}

function getRad(deg: number): number {
  return (deg * Math.PI) / 180
}

function heaviside(x: number): 1 | 0 {
  return x > 0 ? 1 : 0
}

function specifyCoords({ x, y }: any, { width, height }: any, angle: any) {
  const cos = Math.cos(getRad(angle))
  const sin = Math.sin(getRad(angle))

  return {
    x: x + (heaviside(Math.sign(cos)) - 1) * width,
    y: y - heaviside(Math.sign(cos)) * height,
  }
}

function createElement(classname: string) {
  const $el = document.createElement('div')
  $el.classList.add(classname)
  return $el
}

// renders
function renderDot(point: CirclePoint, helpers: any) {
  const { angle, color } = point
  const { radius, $root } = helpers
  const cartesian = polarToCartesian({ angle, radius })
  const coords = { x: cartesian.x - 10, y: cartesian.y - 10 }
  const { x, y } = coordsToPercents(coords, radius)

  const $dot = createElement('dot')
  $dot.style.backgroundColor = color
  $dot.style.left = x + '%'
  $dot.style.bottom = y + '%'

  $root.insertAdjacentElement('afterbegin', $dot)
}

function renderTitle(point: CirclePoint, helpers: any) {
  const { angle, color, title, clockwise } = point
  const { radius, $root } = helpers
  const direction = clockwise ? 1 : -1
  const cartesian = polarToCartesian({
    angle: angle + direction * 2,
    radius: radius + 15,
  })

  const $title = createElement('title')
  $title.innerHTML = title
  $root.insertAdjacentElement('afterbegin', $title)
  const rect = $title.getBoundingClientRect()

  const coords = specifyCoords(cartesian, rect, angle)
  const { x, y } = coordsToPercents(coords, radius)
  $title.style.color = color
  $title.style.left = x + '%'
  $title.style.bottom = y + '%'
}

function renderItems(point: CirclePoint, helpers: any) {
  const { angle, color, items, clockwise } = point
  const { radius, $root } = helpers
  const direction = clockwise ? 1 : -1

  let alpha = angle - direction * 10
  items.forEach((text, index) => {
    const cartesian = polarToCartesian({
      angle: alpha,
      radius: radius + 10,
    })

    const $item = createElement('item')
    $item.innerHTML = text
    $root.insertAdjacentElement('afterbegin', $item)
    const rect = $item.getBoundingClientRect()

    const coords = specifyCoords(cartesian, rect, angle)
    const { x, y } = coordsToPercents(coords, radius)
    $item.setAttribute('data-color', color)
    $item.style.left = x + '%'
    $item.style.bottom = y + '%'

    const coeff = (rect.height - 16) / 16
    alpha = alpha - direction * 7 - direction * coeff * 7

    // item dot
    const $itemDot = createElement('item-dot')
    $itemDot.style.backgroundColor = color
    $item.insertAdjacentElement('afterbegin', $itemDot)
  })
}

function clear() {
  document.querySelector('[data-points]')!.innerHTML = ''
}

function renderPoint(point: CirclePoint, helpers: any) {
  renderDot(point, helpers)
  renderTitle(point, helpers)
  renderItems(point, helpers)
}

function renderOptions(options: CirclePoint[]) {
  const $circle = document.querySelector('[data-circle]')!
  const $root = document.querySelector('[data-points]')!
  const radius = $circle.getBoundingClientRect().width / 2

  const helpers = { radius, $root }

  options.forEach((point) => renderPoint(point, helpers))
}

import Vue from 'vue'
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
  watch: {
    options(value) {
      clear()
      renderOptions(value)
    },
  },
  mounted() {
    renderOptions(this.options)
  },
})
</script>

<style lang="scss" scoped>
.circle {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  padding-top: 0%;
  width: 520px;

  @media screen and (max-width: 900px) {
    position: absolute;
    left: -100px;
    width: 280px;
  }

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

  ::v-deep .points {
    position: absolute;
    bottom: 0;
    right: 0;
    left: 0;
    top: 0;

    .dot {
      position: absolute;
      width: 20px;
      height: 20px;
      border-radius: 100%;
    }

    .title {
      position: absolute;
      font-size: 20px;
      line-height: 27px;
    }

    .item {
      position: absolute;
      font-size: 12px;
      line-height: 16px;
      max-width: 200px;
      width: 100%;
      padding-left: 16px;

      .item-dot {
        position: absolute;
        left: 0;
        top: 6px;
        width: 6px;
        height: 6px;
        border-radius: 100%;
      }
    }
  }
}
</style>