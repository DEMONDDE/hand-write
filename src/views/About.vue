<template>
  <div class="about">
    <canvas id="canvas" class="can" width="600" height="300" ref="canv"
     @mousedown.prevent="mouseDown" @mousemove.prevent="mouseMove"  @mouseup="mouseUp"
     @touchstart.prevent="touchDown" @touchmove.prevent="touchMove" @touchend.prevent="touchUp"></canvas>
    <button @click="save" >保存</button>
    <button @click="init" >清空</button>
    <button @click="back" >回退</button>
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
      isDown: false,
      history: [],
      container: null
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    init() {
      this.container = document.getElementById('canvas')
      this.canvans = this.container.getContext('2d')
      this.canvans.clearRect(0,0, this.container.width, this.container.height);

    },
    mouseDown(event) {
      this.startX = event.offsetX
      this.startY = event.offsetY
      this.saveHistory()
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
        this.saveHistory()
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
      let canvans = document.getElementById('canvas')
      let src =  canvans.toDataURL({format: 'image/png', quality:1, width:14000, height:4000});
      this.download(src)
    },
    download(base64) {
        var date = new Date();

        var aLink = document.createElement("a"); 
        var blob = this.base64ToBlob(base64);
        aLink.download = date.getTime() + "." + blob.type.split("/")[1]; 
        aLink.href = URL.createObjectURL(blob);
        aLink.click();
    },
    // base64转Blob对象
   base64ToBlob(code) {
        var parts = code.split(";base64,");
        var contentType = parts[0].split(":")[1];
        var raw = window.atob(parts[1]);
        var rawLength = raw.length;
        var uint8Array = new Uint8Array(rawLength);
        for (var i = 0; i < rawLength; i++) {
            uint8Array[i] = raw.charCodeAt(i);
        }
        return new Blob([uint8Array], {type: contentType});
      },
    draw(x, y) {
      this.canvans.beginPath();
      this.canvans.moveTo(this.startX, this.startY);
      this.canvans.lineTo(x, y);
      this.canvans.stroke();
      this.points.push({x, y})
    },
    saveHistory() {
      let imageData = this.canvans.getImageData(0, 0, this.container.width, this.container.height)
      this.history.push(imageData)
    },
    back() {
      let imageData = this.history.pop()
      if(imageData) {
        this.canvans.putImageData(imageData, 0, 0)
      }
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
