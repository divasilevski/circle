<template lang="pug">
  .scroll-circle
    
    .circle-container
      CircleShape(:options="oneOption || options")
      //- CircleRender(:options="oneOption || options" :rotate="rotation")
      .here {{ animation }}
    client-only
      MatchMedia(:max-width="900" v-slot="{match}" @change="setObservers")
        .lines
          template(v-if="!match")
            .interception-line(
              v-for="(option, index) in options"
              :key="option.title"
              :data-index="index")


</template>

<script>
const OPTIONS = [
  {
    title: 'Тренировки',
    angle: 55,
    clockwise: true,
    color: '#7448D9',
    items: [
      'укрепите мышцы спины',
      'вернётесь в свою лучшую форму',
      'узнаете, как эффективно тренироваться лёжа',
      'попробуете разное и найдёте вид тренировок, который вам по душе',
      'научитесь тренироваться в удовольствие',
    ],
  },
  {
    title: 'Питание',
    angle: -37,
    clockwise: true,
    color: '#EABA92',
    items: [
      'подберёте режим питания, который подходит именно вам',
      'построите здоровый рацион',
      'узнаете, как перестать переедать и заедать стресс',
    ],
  },
  {
    title: 'Знания',
    angle: -207,
    color: '#BC65CD',
    items: [
      'узнаете, как лучше спать и высыпаться',
      'научитесь находить время на себя, тренировки и здоровое питание',
      'изучите разработки #sekta: базовое состояние, циклы мотивации и New normal',
      'узнаете простые, но эффективные способы ухода за кожей',
    ],
  },
]

import Vue from 'vue'
export default Vue.extend({
  data() {
    return {
      options: OPTIONS,
      animation: null,
      oneOption: null,
    }
  },
  watch: {
    animation(value) {
      if (value !== null) {
        this.oneOption = [
          { ...this.options[value], angle: 60, clockwise: true },
        ]
      }
    },
  },
  computed: {
    rotation() {
      if (this.animation === null) return 'rotate(0deg)'
      return `rotate(${OPTIONS[this.animation].angle - 60}deg)`
    },
  },
  methods: {
    setObservers(value) {
      if (document && !value) {
        setTimeout(() => {
          const $lines = document.querySelector('.lines')

          const options = {
            root: null,
            threshold: 0,
            rootMargin: '-50% 0% -50% 0%',
          }
          const callback = (entries, observer) => {
            entries.forEach((el) => {
              if (el.isIntersecting) {
                this.animation = el.target.dataset.index
              }
            })
          }
          const observer = new IntersectionObserver(callback, options)

          Array.from($lines.children).forEach(($line) => {
            observer.observe($line)
          })
        })
      } else {
        this.animation = null
        this.oneOption = null
      }
    },
  },
})
</script>

<style lang="scss" scoped>
$bounce: 900px;
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
    width: 100%;
    overflow: hidden;

    @media screen and (max-width: $bounce) {
      .circle-shape {
        left: -40%;
        padding: 0;
        margin-right: 30px;
      }
    }

    .here {
      position: absolute;
      top: 0;
      right: 0;
    }

    @media screen and (max-width: $bounce) {
      padding: 0;
      justify-content: flex-start;
    }
  }

  .lines {
    .interception-line {
      @media screen and (max-width: $bounce) {
        width: 100%;
        height: 800px;
      }
    }
  }
}
</style>