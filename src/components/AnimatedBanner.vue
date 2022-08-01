<template>
  <div
    class="animated-banner"
    @mousemove="handleMouseMove"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
    ref="animated-banner"
  >
    <div
      class="layer"
      v-for="({ src, width, height, style, title }, index) in imgs"
      :key="index"
    >
      <img
        :src="src"
        :title="title"
        :style="style"
        :width="width"
        :height="height"
        alt=""
      />
    </div>
    <div class="taper-line"></div>
    <canvas ref="canvas" :width="wrapperWidth"></canvas>
  </div>
</template>

<script>
import TWEEN from "tween.js";
const petal0 = new Image();
const petal1 = new Image();
petal0.src = require(`@/assets/banner/petal0.png`);
petal1.src = require(`@/assets/banner/petal1.png`);

export default {
  name: "animated-banner",
  data() {
    return {
      imgOptions: [
        {
          width: 5612,
          height: 209,
          rate: { opacity: 1, offsetX: 0.075 },
          default: { offsetX: 0, offsetY: 0, opacity: 1 },
          blur: 0,
        },
        {
          width: 5612,
          height: 209,
          rate: { opacity: 1, offsetX: 0.075 },
          default: { offsetX: 1277.42, offsetY: 2.16, opacity: 1 },
          blur: 0,
        },
        {
          width: 1841,
          height: 188,
          rate: { opacity: 1, offsetX: 0.1 },
          default: { offsetX: 783.87, offsetY: 0, opacity: 1 },
          blur: 0,
        },
        {
          width: 1671,
          height: 204,
          rate: { opacity: 1, offsetX: 0.2 },
          default: { offsetX: -739.74, offsetY: 0, opacity: 1 },
          blur: 0,
        },
        {
          width: 290,
          height: 72,
          rate: { opacity: 1, offsetX: 0.075 },
          default: { offsetX: 705.48, offsetY: 52.25, opacity: 1 },
          blur: 0,
        },
        {
          width: 238,
          height: 98,
          rate: { opacity: 2, offsetX: -0.06 },
          default: { offsetX: 292.64, offsetY: 42.27, opacity: 0 },
          blur: 0,
          opacityDirection: 1,
        },
        {
          width: 1428,
          height: 146,
          rate: { opacity: 1, offsetX: 0.3 },
          default: { offsetX: 130.06, offsetY: 16.25, opacity: 1 },
          blur: 0,
        },
        {
          width: 1428,
          height: 94,
          rate: { opacity: 1, offsetX: 0.5 },
          default: { offsetX: -406.45, offsetY: 56.9, opacity: 1 },
          blur: 0,
        },
        {
          width: 230,
          height: 160,
          rate: { opacity: 2, offsetX: 0.1 },
          default: { offsetX: -278.71, offsetY: 18.58, opacity: 0 },
          blur: 0,
          opacityDirection: -1,
        },
        {
          width: 67,
          height: 118,
          rate: { opacity: 2, offsetX: 0.25 },
          default: { offsetX: -394.83, offsetY: 37.16, opacity: 0 },
          blur: 0,
          opacityDirection: -1,
        },
        {
          width: 314,
          height: 132,
          rate: { opacity: 1, offsetX: 0.9 },
          default: { offsetX: -104.51, offsetY: 15.67, opacity: 1 },
          blur: 0,
        },
        {
          width: 2483,
          height: 209,
          rate: { opacity: 1, offsetX: 0.9 },
          default: { offsetX: 116.12, offsetY: 0, opacity: 1 },
          blur: 0,
        },
        {
          width: 487,
          height: 170,
          rate: { opacity: 1, offsetX: 0.9 },
          default: { offsetX: 250.83, offsetY: 15.67, opacity: 1 },
          blur: 0,
          opacityDirection: 0,
        },
        {
          width: 515,
          height: 245,
          rate: { opacity: 1, offsetX: 1.1 },
          default: { offsetX: 2438.71, offsetY: 0, opacity: 1 },
          blur: 1,
        },
        {
          width: 1112,
          height: 209,
          rate: { opacity: 1, offsetX: 2 },
          default: { offsetX: -1174, offsetY: 0, opacity: 1 },
          blur: 1,
        },
      ],
      enterPostionX: 0,
      postionX: 0,
      wrapperWidth: 1920,
      petalsOption: [],
    };
  },
  props: {},
  computed: {
    imgs() {
      return this.imgOptions.map(
        (
          {
            width,
            height,
            rate: { opacity: opacityRate, offsetX: offsetXRate },
            default: {
              offsetX: defaultOffsetX,
              offsetY: defaultOffsetY,
              opacity: defaultOpacity,
            },
            blur,
            opacityDirection,
          },
          k
        ) => {
          let offsetOpacity = 0;
          const d = this.offsetX >= 0 ? 1 : -1;

          if (opacityDirection) {
            if (d === opacityDirection) {
              offsetOpacity = (d * this.offsetX) / this.wrapperWidth;
            }
          } else if (opacityDirection === 0) {
            offsetOpacity = -Math.abs(this.offsetX / this.wrapperWidth);
          }

          return {
            src: require(`@/assets/banner/${k}.png`),
            width,
            height,
            style: {
              transform: `scale(1) translate(${defaultOffsetX -
                this.offsetX *
                  offsetXRate}px, ${defaultOffsetY}px) rotate(0deg)`,
              opacity: defaultOpacity + offsetOpacity * opacityRate,
              filter: `blur(${blur}px)`,
            },
          };
        }
      );
    },
    offsetX() {
      return this.postionX - this.enterPostionX;
    },
  },
  watch: {},
  created() {
    this.petalsOption = [...Array(40)].map((v) => {
      return this.generatePetalOption();
    });
  },
  mounted() {
    this.wrapperWidth = this.$refs[
      "animated-banner"
    ].getBoundingClientRect().width;
    this.drawPetals([petal0, petal1]);
  },
  methods: {
    handleMouseMove({ x }) {
      this.postionX = x;
    },
    handleMouseEnter({ x }) {
      this.enterPostionX = x;
    },
    handleMouseLeave() {
      this.animateInitX();
    },
    animateInitX() {
      new TWEEN.Tween(this)
        .to({ postionX: this.enterPostionX }, 100)
        .easing(TWEEN.Easing.Linear.None)
        .start();
      this.tweenAnimate();
    },
    tweenAnimate(time) {
      requestAnimationFrame(this.tweenAnimate);
      TWEEN.update(time);
    },
    drawPetals(petals) {
      const draw = () => {
        const canvas = this.$refs.canvas;
        var ctx = canvas.getContext("2d");
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        this.petalsOption.map(({ x, y, size, speed, angel }, index) => {
          this.petalsOption[index].x =
            x + speed * Math.sin((angel / 180) * Math.PI);
          this.petalsOption[index].y =
            y + speed * Math.cos((angel / 180) * Math.PI);
          const image = angel % 2 === 0 ? petals[0] : petals[1];
          if (x > canvas.width || y > canvas.height) {
            this.petalsOption[index] = this.generatePetalOption();
          }
          return ctx.drawImage(image, x, y, size, size);
        });

        window.requestAnimationFrame(draw);
      };
      window.requestAnimationFrame(draw);
    },
    generatePetalOption() {
      const z = this.getRandomNumber(1, 10) / 10;
      let size = this.getRandomNumber(4, 6) / z;
      size = size < 5 ? 5 : size;
      size = size > 18 ? 18 : size;
      return {
        x: this.getRandomNumber(0, 1920),
        y: -this.getRandomNumber(20, 100),
        size,
        speed: (this.getRandomNumber(1, 4) * size) / 8,
        angel: this.getRandomNumber(30, 60),
      };
    },
    getRandomNumber(n, m) {
      return Math.floor(Math.random() * (m - n + 1) + n);
    },
  },
  components: {},
};
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
  overflow: hidden;
  user-select: none;
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
