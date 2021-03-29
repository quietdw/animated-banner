<template>
  <div
    class="animated-banner"
    @mousemove="handleMouseMove"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
    ref="animated-banner"
  >
    <div class="layer" v-for="({ src, width, height, style }, index) in imgs" :key="index">
      <img :src="src" :style="style" :width="width" :height="height" alt="" />
    </div>
    <div class="taper-line"></div>
    <canvas></canvas>
  </div>
</template>

<script>
import TWEEN from 'tween.js'

export default {
  name: 'animated-banner',
  data() {
    return {
      sizeOption: [
        { width: 5612, height: 209, opacityRate: 1, offsetXRate: 0.075 },
        { width: 5612, height: 209, opacityRate: 1, offsetXRate: 0.075 },
        { width: 1841, height: 188, opacityRate: 1, offsetXRate: 0.1 },
        { width: 1671, height: 204, opacityRate: 1, offsetXRate: 0.2 },
        { width: 290, height: 72, opacityRate: 1, offsetXRate: 0.075 },
        { width: 238, height: 98, opacityRate: 2, offsetXRate: -0.06 },
        { width: 1428, height: 146, opacityRate: 1, offsetXRate: 0.3 },
        { width: 1428, height: 94, opacityRate: 1, offsetXRate: 0.5 },
        { width: 230, height: 160, opacityRate: 2, offsetXRate: 0.1 },
        { width: 67, height: 118, opacityRate: 2, offsetXRate: 0.25 },
        { width: 314, height: 132, opacityRate: 1, offsetXRate: 0.9 },
        { width: 2483, height: 209, opacityRate: 1, offsetXRate: 0.9 },
        { width: 487, height: 170, opacityRate: 1, offsetXRate: 0.9 },
        { width: 515, height: 245, opacityRate: 1, offsetXRate: 1.1 },
        { width: 1112, height: 209, opacityRate: 1, offsetXRate: 2 }
      ],
      defaultOffsetX: {
        0: 0,
        1: 1277.42,
        2: 783.87,
        3: -739.74,
        4: 705.48,
        5: 292.64,
        6: 130.06,
        7: -406.45,
        8: -278.71,
        9: -394.83,
        10: -104.51,
        11: 116.12,
        12: 250.83,
        13: 2438.71,
        14: -1174
      },
      defaultOffsetY: {
        0: 0,
        1: 2.16,
        2: 0,
        3: 0,
        4: 52.25,
        5: 42.27,
        6: 16.25,
        7: 56.9,
        8: 18.58,
        9: 37.16,
        10: 15.67,
        11: 0,
        12: 15.67,
        13: 0,
        14: 0
      },
      defaultOpacity: {
        0: 1,
        1: 1,
        2: 1,
        3: 1,
        4: 1,
        5: 0,
        6: 1,
        7: 1,
        8: 0,
        9: 0,
        10: 1,
        11: 1,
        12: 1,
        13: 1,
        14: 1
      },
      enterPostionX: 0,
      postionX: 0,
      wrapperWidth: 1920
    }
  },
  props: {},
  computed: {
    imgs() {
      return this.sizeOption.map(({ width, height, opacityRate, offsetXRate }, k) => {
        let offsetOpacity = 0
        if ([8, 9].indexOf(k) >= 0 && this.offsetX < 0) {
          offsetOpacity = -this.offsetX / this.wrapperWidth
        }
        if ([5].indexOf(k) >= 0 && this.offsetX > 0) {
          offsetOpacity = this.offsetX / this.wrapperWidth
        }
        if ([12].indexOf(k) >= 0) {
          offsetOpacity = -this.getPositiveNumber(Math.abs(this.offsetX / this.wrapperWidth))
        }
        return {
          src: require(`@/assets/banner/${k}.png`),
          width,
          height,
          style: {
            transform: `scale(1) translate(${this.defaultOffsetX[k] -
              this.offsetX * offsetXRate}px, ${this.defaultOffsetY[k]}px) rotate(0deg)`,
            opacity: this.defaultOpacity[k] + offsetOpacity * opacityRate
          }
        }
      })
    },
    offsetX() {
      return this.postionX - this.enterPostionX
    }
  },
  watch: {},
  created() {},
  mounted() {
    this.wrapperWidth = this.$refs['animated-banner'].getBoundingClientRect().width
  },
  methods: {
    handleMouseMove({ x }) {
      this.postionX = x
    },
    handleMouseEnter({ x }) {
      this.enterPostionX = x
    },
    handleMouseLeave() {
      this.moveX()
    },
    moveX() {
      new TWEEN.Tween(this)
        .to({ postionX: this.enterPostionX }, 100)
        .easing(TWEEN.Easing.Linear.None)
        .start()
      this.animate()
    },
    getPositiveNumber(v) {
      if (v < 0) {
        return -v
      }
      return v
    },
    animate(time) {
      requestAnimationFrame(this.animate)
      TWEEN.update(time)
    }
  },
  components: {}
}
</script>
<style lang="scss" scoped>
.animated-banner {
  min-width: 999px;
  height: 180px;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  background: red;
  overflow: hidden;
  .layer {
    position: absolute;
    left: 0;
    top: 0;
    height: 100%;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .taper-line {
    pointer-events: none;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 0;
    width: 100%;
    height: 100px;
    background: linear-gradient(rgba(0, 0, 0, 0.4), transparent);
  }
  canvas {
    position: absolute;
    top: 0px;
    left: 0px;
    width: 100%;
    height: 180px;
  }
}
</style>
