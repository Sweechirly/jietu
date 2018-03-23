<template>
  <div class="hello">
    <div class="shadow" :style="shadow">
      <img v-if="shadow.filter" src="../assets/桥梁绿化1 .jpg" alt="" width="100%" height="100%">
    </div>
    <div class="move" v-drag ref="div" :style="`width:${width}px;height:${height}px;transform: scale(${scaleX},${scaleY}) translate3d(${translateX}px, ${translateY}px, 0px) rotate(${rotate}deg);cursor:move`" @mousewheel="changeSize($event)">
      <img :src="imgurl" ref="img" height="100%"   alt="">
    </div>
    <div class="frame" v-if="type" :style="`top:${verTop}px;width:${verFrameWidth}px;height:${verFrameHeight}px;left:${verLeft}px`">
      <!-- <img src="../assets/Bitmapce.png" alt=""> -->
      <div class="virtual" :style="`top:${-verTop}px;left:${-verLeft}px`">
        <div class="move" v-drag ref="div" :style="`width:${width}px;height:${height}px;transform: scale(${scaleX},${scaleY}) translate3d(${translateX}px, ${translateY}px, 0px) rotate(${rotate}deg);cursor:move`" @mousewheel="changeSize($event)">
          <img id="img" :src="imgurl" ref="img" height="100%"   alt="">
        </div>
      </div>
    </div>
    <div class="frame" v-if="!type" :style="`top:${transTop}px;width:${tranFrameWidth}px;height:${tranFrameHeight}px;left:${transLeft}px`">
      <!-- <img src="../assets/Bitmapce.png" alt=""> -->
      <div class="virtual" :style="`top:${-transTop}px;left:${-transLeft}px`">
        <div class="move" v-drag ref="div" :style="`width:${width}px;height:${height}px;transform: scale(${scaleX},${scaleY}) translate3d(${translateX}px, ${translateY}px, 0px) rotate(${rotate}deg);cursor:move`" @mousewheel="changeSize($event)">
          <img :src="imgurl" ref="img" height="100%" alt="">
        </div>
      </div>
    </div>
    <div class="canvas" v-if="canvas">
      <canvas ref="canvas" :width="canvasWidth" :height="canvasHeight"></canvas>
    </div>
    <div class="button">
      <button @click="addPic">添加图片</button>
      <button @click="rotateFrame">旋转画框</button>
      <button @click="adaptWidth">宽度自适应</button>
      <button @click="adaptHeight">高度自适应</button>
      <button @click="changeBack(0)">黑背景</button>
      <button @click="changeBack(1)">白背景</button>
      <button @click="changeBack(2)">毛玻璃效果</button>
      <button @click="drawImage">绘图</button>
    </div>
  </div>
</template>

<script>
import pic from "../assets/桥梁绿化1 .jpg"
export default {
  name: "HelloWorld",
  data() {
    return {
      width: 700,
      height: 300,
      imgurl: "",
      scaleX: 1,
      scaleY: 1,
      translateX: 0,
      translateY: 0,
      rotate: 0,
      marginLeft:0,
      // top:2,
      verTop:60,
      verLeft:203,
      transTop:30,
      transLeft:282,
      verFrameWidth:"294",
      verFrameHeight:"182",
      tranFrameWidth:"136",
      tranFrameHeight:"240",
      type:false,
      originalWidth:"",
      originalHeight:"",
      shadow:{
        background:"",
        filter:"",
      },
      canvasWidth:"",
      canvasHeight:"",
      canvas:false
    };
  },
  methods: {
    addPic() {
      this.imgurl = require("../assets/桥梁绿化1 .jpg");
      // this.$nextTick(()=>{
      //   this.width = this.$refs.img.offsetWidth
      //   this.height = this.$refs.img.getBoundingClientRect().height
      //   this.originalWidth = this.$refs.img.getBoundingClientRect().width
      //   this.originalHeight = this.$refs.img.getBoundingClientRect().height
      //   // this.translateX = (700-this.$refs.img.offsetWidth)/2
      //   console.log(this.$refs.img.getAttribute("width"))
      //   console.log(this.originalHeight)
      // })
      setTimeout(() => {
        this.width = this.$refs.img.offsetWidth
        this.height = this.$refs.img.getBoundingClientRect().height
        this.originalWidth = this.$refs.img.getBoundingClientRect().width
        this.originalHeight = this.$refs.img.getBoundingClientRect().height
        this.translateX = (700-this.$refs.img.offsetWidth)/2
      }, 100);
    },
    changeSize(e) {
       var transform = this.$refs.div.style.transform;
        var scale = transform.match(/[^\(\)]+/g)[1].split(",")
        var translate = transform.match(/[^\(\)]+/g)[3].split(",")
        this.scaleX = Number(scale[0].trim()),
        this.scaleY = Number(scale[1].trim()),
        this.translateX = translate[0].trim().replace("px",""),
        this.translateY = translate[1].trim().replace("px","");
      if (this.imgurl) {
        if (e.wheelDelta > 0) {
            this.scaleX += 0.1;
            this.scaleY += 0.1;
        } else {
          if (this.scaleX > 0.2) {
            this.scaleX -= 0.1;
            this.scaleY -= 0.1;
          }
        }
      }
    },
    rotateFrame(){
      this.type = !this.type
    },
    adaptWidth(){
      if(!this.type){
        this.scaleX = 136/this.originalWidth;
        this.scaleY = this.scaleX
      }else{
        this.scaleX = 294/this.originalWidth;
        this.scaleY = this.scaleX
        this.translateX = 147/this.scaleX
      }
      this.translateX = ((700-this.$refs.img.offsetWidth)/2)/this.scaleX
    },
    adaptHeight(){
      if(this.type){
        this.scaleY = 182/this.originalHeight;
        this.scaleX = this.scaleY
      }else{
        this.scaleY = 240/this.originalHeight;
        this.scaleX = this.scaleY
      }
      this.translateX = ((700-this.$refs.img.offsetWidth)/2)/this.scaleX
    },
    changeBack(val){
      switch (val) {
        case 0:
          this.shadow.background = "black"
          break;
        case 1:
          this.shadow.background = "white"
          break;
      
        default:
          this.shadow.filter = "blur(5px)"
          break;
      }
    },
    drawImage(){
      this.canvasWidth = 136
      this.canvasHeight = 240
      this.canvas = true;
      this.$nextTick(()=>{
        var canvas = this.$refs.canvas;
        var ctx = canvas.getContext("2d");
        var image = this.$refs.img
        console.log(ctx)
        ctx.drawImage(image,400,50,136,240,0,0,136,240);
      })
    }
  },
  directives: {
    drag(el, update) {
      var oDiv = el;
      var move = document.getElementsByClassName("move")
      oDiv.onmousedown = function(ev) {
        ev.preventDefault()
        var disX = ev.clientX;
        var disY = ev.clientY;
        var transform = oDiv.style.transform;
        var scale = transform.match(/[^\(\)]+/g)[1].split(",")
        var translate = transform.match(/[^\(\)]+/g)[3].split(",")
        var scaleX = scale[0].trim(),
            scaleY = scale[1].trim(),
            translateX = translate[0].trim().replace("px",""),
            translateY = translate[1].trim().replace("px","");
        
        document.onmousemove = function(ev) {
          ev.preventDefault()
          var l = ev.clientX - disX;
          var t = ev.clientY - disY; 
          var x = Number(translateX)+Number(l)
          var y = Number(translateY)+Number(t)
          move[0].style.transform = `scale(${scaleX},${scaleY}) translate3d(${x}px,${y}px,0px)`
          move[1].style.transform = `scale(${scaleX},${scaleY}) translate3d(${x}px,${y}px,0px)`
        };
        document.onmouseup = function() {
          document.onmousemove = null;
          document.onmouseup = null;
        };
      };
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello {
  width: 700px;
  height: 300px;
  border: 1px solid red;
  margin: 0 auto;
  position: relative;
}
.bg {
  width: 100px;
  height: 100px;
  transform: translate3d(0,0,0)
}
.button {
  margin: 0 auto;
}
.frame{
  position: absolute;
  overflow: hidden;
  /* border: 10px solid #dbdbdb; */
  outline: 10px solid #dbdbdb;
  box-shadow: 0 0 0 20px #e4b56e;   
  font-size: 0;
}
.virtual{
  position: absolute;
  width: 700px;
  height: 300px;
  border: 1px solid red;
}
.shadow{
  position: absolute;
  width: 100%;
  height: 100%;
  z-index: 0;
}
</style>
