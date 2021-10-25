<template lang="pug">
  .scroll-circle
    .circle-container
      .circle
        img(src="/circle.png" alt="circle")
        .circle-inner

        .points(data-points)
    
    .interception-line


</template>

<script>
const LABELS_EXPAND = 0 // Дополнительное увеличение ширины лейблов
const LABELS_EXPAND2 = 0 // Дополнительное увеличение ширины лейблов

function getRad(deg) {
  return (deg * Math.PI) / 180
}

function heaviside(x) {
  return x > 0 ? 1 : 0
}

function specifyCoords({ x, y }, { width, height }, angle) {
  const cos = Math.cos(getRad(angle))
  const sin = Math.sin(getRad(angle))

  const center = {
    x: (width + LABELS_EXPAND) / 2,
    y: (height + LABELS_EXPAND2) / 2,
  }
  return {
    x: x + (heaviside(Math.sign(cos)) - 1) * width,
    //  - center.x + cos * center.x + LABELS_EXPAND / 2,
    y: y - heaviside(Math.sign(cos)) * height,
    // - center.y + sin * center.y + LABELS_EXPAND2 / 2,
  }
}

const RADIUS = 260

const ELEM = {
  title: 'Тренировки',
  angle: 70,
  clockwise: 1,
  items: [
    'укрепите мышцы спины',
    'вернётесь в свою лучшую форму',
    'узнаете, как эффективно тренироваться лёжа',
    'попробуете разное и найдёте вид тренировок, который вам по душе',
  ],
}

import Vue from 'vue'
export default Vue.extend({
  mounted() {
    const $circle = document.querySelector('.scroll-circle')
    const $line = document.querySelector('.interception-line')
    const options = {
      root: null,
      threshold: [0.0, 1.0],
    }
    const callback = function (entries, observer) {
      entries.forEach((el) => {
        // if (el.intersectionRatio === 1) {
        //   $circle.style.position = 'sticky'
        // } else {
        //   $circle.style.position = 'relative'
        // }
        console.log(el.intersectionRatio)
      })
    }
    const observer = new IntersectionObserver(callback, options)

    observer.observe($line)

    // -======================================- //
    const $points = document.querySelector('[data-points]')
    const $title = document.createElement('div')
    $title.innerHTML = ELEM.title

    const coords = {
      x: RADIUS * Math.cos(getRad(ELEM.angle)),
      y: RADIUS * Math.sin(getRad(ELEM.angle)),
    }

    $title.style.position = 'absolute'

    $points?.insertAdjacentElement('afterbegin', $title)

    const rect = $title.getBoundingClientRect()
    const c = specifyCoords(coords, rect, ELEM.angle)

    $title.style.left = c.x + 'px'
    $title.style.bottom = c.y + 'px'

    // Items
    const d = ELEM.clockwise ? -1 : 1
    let a = ELEM.angle + 20 * d
    ELEM.items.forEach((text, index) => {
      const $item = document.createElement('div')
      $item.innerHTML = text
      $item.style.position = 'absolute'
      $item.style.width = 'max-content'
      $item.style.maxWidth = '180px'
      $points?.insertAdjacentElement('afterbegin', $item)

      const coords = {
        x: RADIUS * Math.cos(getRad(a)),
        y: RADIUS * Math.sin(getRad(a)),
      }
      const rect = $item.getBoundingClientRect()
      const c = specifyCoords(coords, rect, a)

      const an = (rect.height / RADIUS / Math.PI) * 180
      a = a + d * an + d * 7

      $item.style.width = rect.width + 'px'
      $item.style.left = c.x + 'px'
      $item.style.bottom = c.y + 'px'
    })
  },
})
</script>

<style lang="scss" scoped>
.scroll-circle {
  position: relative;
  display: flex;
  justify-content: center;

  .circle-container {
    position: sticky;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
  .circle {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    max-width: 520px;
    max-height: 520px;

    img {
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
      border-radius: 520px;
    }

    .points {
      position: absolute;
      bottom: 50%;
      right: 50%;
      left: 50%;
      top: 50%;
    }
  }

  .interception-line {
    height: 3000px;
  }
}
</style>