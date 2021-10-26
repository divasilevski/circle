<script lang="ts">
import Vue, { VNode } from 'vue'

export default Vue.extend({
  props: {
    maxWidth: {
      type: Number,
      required: true,
    },
  },
  data() {
    return { match: true }
  },
  fetch() {
    // SSR: match starts with false on mobile devices
    if (this.$nuxt.context.req) {
      const userAgent = this.$nuxt.context.req.headers['user-agent']!
      this.setMatch(!/Android|iPhone/g.test(userAgent))
    }
  },
  mounted() {
    if (process.browser) {
      this.setMatch(window.innerWidth > this.maxWidth)

      const mediaQueryMax = window.matchMedia(`(max-width: ${this.maxWidth}px)`)
      const checkMatches = (event: any) => this.setMatch(!event.matches)

      mediaQueryMax.onchange = checkMatches
      this.$once('hook:beforeDestroy', () => {
        mediaQueryMax.onchange = null
      })
    }
  },
  methods: {
    setMatch(value: boolean) {
      this.match = value
      this.$emit('change', value)
    },
  },
  render(h): VNode {
    return h('div', {}, this.$scopedSlots.default!({ match: this.match }))
  },
})
</script>
