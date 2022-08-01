# 预览

   [预览地址](https://jiangnana.fun/animated-banner/) 

# 原理

1. 背景由多个静态 png 图片构成；
2. 背景图片的层级由远及进堆叠，类似 Photoshop 的图层；
3. 监听鼠标移动事件，控制背景图片移动，图片层级高度越高越快，反之越慢；
4. 最后使用用 Canvas 增加花瓣动画效果。

# 伪代码

1. 布局背景图片
2. 添加鼠标事件，图层随着鼠标移动
3. 鼠标移开回到初始布局
4. 生成一定数量花瓣，每个花瓣有随机出现位置、大小、移动角度、移动速度
5. 当花瓣移动到画布外，重新生成一个新的花瓣位置

# 代码实现

1. 布局背景图片

定义一个配置项，定义图片大小、位置、移动速度、透明度更新速度、初始透明度、初始位置、是否模糊、图片出现或者消失，配置好以后 `v-for` 一把梭；


```javascript
 [
    {
      width: 5612,
      height: 209,
      rate: { opacity: 1, offsetX: 0.075 },
      default: { offsetX: 0, offsetY: 0, opacity: 1 },
      blur: 0,
      opacityDirection:0
   },
   ...
 ]
```

2. 添加鼠标事件，图层随着鼠标移动 

保存鼠标移入时的x坐标以及鼠标移入后实时x坐标，通过两者差值，判断左右移动然后根据定义好的图片移动速度，更新图片的位置，使用 `tween.js` 平滑更新 offsetX 值；

```javascript
{
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
}
```

3. 生成一定数量花瓣，每个花瓣有随机出现位置、大小、移动角度、移动速度；

生成长度40的数组，然后随机生成花瓣的信息，塞入，使用 `generatePetalOption` 函数生成信息。

```javascript
function generatePetalOption() {
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
}
```

```javascript
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
      //当花瓣移动到画布外，重新生成一个新的花瓣位置
      this.petalsOption[index] = this.generatePetalOption(); 
      }
      return ctx.drawImage(image, x, y, size, size);
   });

   window.requestAnimationFrame(draw);
};

window.requestAnimationFrame(draw);
}
```
 
然后使用 drawImage API，将40个花瓣渲染到 canvas 上，requestAnimationFrame 允许我们在下次屏幕刷新前更新 canvas 从而实现花瓣的动画效果


















