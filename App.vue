<template>
<div>
  hello
  <div class="box" ref="box"></div>
  <div class="canvas" ref="canvas"></div>
</div>
</template>

<script>
import { gsap, TimelineLite, Back, Elastic, Expo } from 'gsap'
import { MotionPathPlugin } from 'gsap/MotionPathPlugin'
import { SVG } from '@svgdotjs/svg.js'

gsap.registerPlugin(MotionPathPlugin)

const spiral =
  'm 357.64532,453.84097 c 17.62007,8.02216 -2.12058,27.70935 -13.33334,29.28571 -30.3859,4.27185 -48.34602,-29.97426 -45.23807,-55.9524 5.5594,-46.46879 56.1311,-70.59787 98.57145,-61.19043 62.28294,13.8058 93.32728,82.57702 77.1428,141.19051 C 453.21679,585.29693 365.67122,623.42358 290.97859,600.26951 196.98554,571.13248 151.71003,464.56996 181.93108,373.84089 218.53281,263.95583 344.23687,211.49702 450.97875,248.84102 576.77037,292.84963 636.43303,437.76771 591.93099,560.50775 540.55162,702.21597 376.3736,769.09583 237.6452,717.41234 80.01319,658.68628 5.9069261,475.21736 64.788247,320.50751 130.84419,146.94643 333.62587,65.607117 504.31214,131.69819 693.80625,205.0718 782.38357,427.18225 709.07382,613.84113'

export default {
  data() {
    return {
      canvas: null,
      timeline: null,
    }
  },
  mounted() {
    const { box } = this.$refs
    this.canvas = SVG()
      .addTo(this.$refs.canvas)
      .size('100%', '100%')
      .viewbox(0, 0, 800, 1000)
    const path = this.canvas
      .path(spiral)
      .fill('none')
      .stroke({ width: 1, color: '#ccc' })
    const rect = this.canvas.rect(100, 100).attr({ fill: '#f06' })
    rect
    path
    this.timeline = new TimelineLite({
      onComplete: () => this.timeline.restart(),
    })
    this.timeline.to(box, 1, { x: 200, rotation: 90, ease: Back.easeInOut })
    this.timeline.to(box, 0.5, { background: 'green' }, '-=0.5')
    this.timeline.to(box, 0.5, { background: 'yellow' })

    const dot = this.createDot(400, 400, 100)
    const dotTimeline = new TimelineLite({
      onComplete: () => dotTimeline.restart(),
    })
    console.log(dot.node, box)
    dotTimeline.to(dot.node, 0.5, {
      scale: 1.2,
      transformOrigin: '50% 50%',
      ease: Back.easeInOut,
    })
    dotTimeline.to(dot.node, 0.5, {
      scale: 1,
      transformOrigin: '50% 50%',
      ease: Back.easeInOut,
    })
    dotTimeline.to(dot.node, 0.5, { fill: 'red' })
    gsap.to(dot, {
      motionPath: {
        path: spiral,
        align: 'self',
      },
      duration: 5,
    })

    const dotSize = 20
    const spaceBetweenDots = dotSize * 2
    const interval = 20
    let dotsPlaced = 0

    const place = () => {
      const distanceOnPath = dotsPlaced * spaceBetweenDots
      if (distanceOnPath > path.length()) return
      const location = path.pointAt(distanceOnPath)
      console.log(location)
      const d = this.createDot(location.x, location.y, dotSize)
      this.animateDot(d)
      dotsPlaced++
      setTimeout(place, interval)
    }
    setTimeout(place, interval)
  },
  methods: {
    createDot(x, y, diameter) {
      const dot = this.canvas.circle(diameter).move(x, y)
      dot.node.classList.add('dot')
      return dot
    },
    animateDot(dot) {
      const dotTimeline = new TimelineLite({
        onComplete: () => dotTimeline.restart(),
      })
      dotTimeline.to(dot.node, 1.5, {
        scale: 1.2,
        fill: 'red',
        transformOrigin: '50% 50%',
        ease: Elastic.easeOut.config(2.5, 0.5),
      })
      // dotTimeline.to(dot.node, 0.5, {
      //   scale: 1,
      //   transformOrigin: '50% 50%',
      //   ease: Elastic.easeOut.config(2.5, 0.5),
      // })
    },
  },
}
</script>

<style lang="stylus">
.box
  width: 60px
  height: 60px
  background: red
.dot
  fill: #dd0000
  transformOrigin: 50% 50%
</style>
