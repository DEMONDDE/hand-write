<template>
  <div class="about">
    <canvas id="canvas" class="can" width="300" height="300" ref="canv"
     @mousedown.prevent="mouseDown" @mousemove.prevent="mouseMove"  @mouseup="mouseUp"
     @touchstart.prevent="touchDown" @touchmove.prevent="touchMove" @touchend.prevent="touchUp"></canvas>
    <button @click="save" >保存</button>
    <button @click="init" >清空</button>
  </div>
</template>
<script>


export default {
  name: 'handWriting',
  data() {
    return {
      canvans: null,
      startX: 0,
      startY: 0,
      points: [],
      isDown: false
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    init() {
      let canvans = document.getElementById('canvas')
      this.canvans = canvans.getContext('2d')
      this.canvans.clearRect(0,0,canvans.width,canvans.height);

    },
    mouseDown(event) {
      this.startX = event.offsetX
      this.startY = event.offsetY
      this.draw(this.startX, this.startY)
      this.isDown = true
    },
    mouseMove(event) {
      if(this.isDown) {
        let x  = event.offsetX
        let y = event.offsetY
        this.draw(x, y)
        this.startX = x
        this.startY = y
      }
    },
    mouseUp(event) {
      if(this.isDown) {
        let x  = event.offsetX
        let y = event.offsetY
        this.draw(x, y)
        this.startX = 0
        this.startY = 0
        this.isDown = false
      }
    },
    touchDown(ev) {
      ev.preventDefault();
      ev = ev || event;
      if (ev.touches.length == 1) {
        let x = ev.targetTouches[0].clientX - ev.target.offsetLeft
        let y = ev.targetTouches[0].clientY - ev.target.offsetTop
      this.startX = x
      this.startY = y
      this.draw(this.startX, this.startY)
      this.isDown = true
      }
    },
    touchMove(ev) {
      ev.preventDefault();

      ev = ev || event;
      ev.preventDefault();
      if (ev.touches.length == 1 && this.isDown) {
        let x = ev.targetTouches[0].clientX - ev.target.offsetLeft
        let y = ev.targetTouches[0].clientY - ev.target.offsetTop
        this.draw(x, y)
        this.startX = x
        this.startY = y
      }
    },
    touchUp(ev) {
      ev = ev || event;
      ev.preventDefault();
      if(ev.touches.length == 1 && this.isDown) {
        let x = ev.targetTouches[0].clientX - ev.target.offsetLeft
        let y = ev.targetTouches[0].clientY - ev.target.offsetTop
        this.draw(x, y)
        this.startX = 0
        this.startY = 0
        this.isDown = false
      }
    },
    save() {
      let image =  new  Image();
      let canvans = document.getElementById('canvas')

      image.src =  canvans.toDataURL({format: 'image/png', quality:1, width:14000, height:4000});
      var url = image.src.replace(/^data:image\/[^;]/, 'data:application/octet-stream');
      window.open(url);

    },
    draw(x, y) {
      this.canvans.beginPath();
      this.canvans.moveTo(this.startX, this.startY);
      this.canvans.lineTo(x, y);
      this.canvans.stroke();
      this.points.push({x, y})
    }
  }
}
</script>
<style scoped>
  .can{
    display: block;
    border: 1px solid black;
  }
</style>
