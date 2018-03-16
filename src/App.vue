<template>
  <div id="hw-preview_img_wrap">
    <h6 class="title">{{title}}</h6>
    <i class="icon-close pointer"></i>
    <div class="pre_img_content">
      <div class="pre_img_con_cur">
        <i class="icon icon-arrow-l" @click.stop="pre"></i>
        <div :class="['pre_img_cur',{'pre_img_cur_move':is_move}]"  ref="pre_img_content">
           <preview-img
             :url="url"
             :scale="scale"
             :rotate="rotate"
             :top.sync="top"
             :left.sync="left"
             :preImgCss ="trans"
             :initWidth.sync="i_w"
             :i_top.sync="i_top"
             :i_left.sync="i_left"
             :initHeight.sync="i_h"
             :initTop.sync="initTop"
             :updatePos.sync="updatePos"
             :initLeft.sync="initLeft">
             <!--<i class="loading_icon icon-load1" slot="load"></i>-->
           </preview-img>
        </div>
        <i class="icon icon-arrow-r" @click.stop="suf"></i>
      </div>
    </div>
    <div :class="['pre_img_con_fn',{pre_img_con_fn_active :imgs_srpead }]">
      <template v-if="imgs_srpead">
        <span class="fn_module pointer fn_spread" @click.stop="imgs_srpead = false">
           <i class="icon icon-arrow-down"></i>
            收起列表
       </span>
      </template>
      <template v-else>
       <span class="fn_module pointer" @click.stop="imgs_srpead = true">
           <i class="icon icon-arrow-top"></i>
            展开列表
       </span>
      </template>
      <span class="fn_module pointer">
           <i class="icon icon-play"></i>
            自动播放
       </span><span class="fn_module pointer" @click.stop="bigger">
           <i class="icon icon-scale-up"></i>
            放大
       </span><span class="fn_module pointer" @click.stop="smaller">
           <i class="icon icon-scale-down"></i>
            缩小
       </span><span class="fn_module pointer" @click.stop="spin">
           <i class="icon icon-rotate"></i>
            旋转
       </span><span class="fn_module pointer" @click.stop="init">
           <i class="icon icon-init"></i>
            复位
       </span>
    </div>
    <div class="pre_img_con_list" v-show="imgs_srpead">
      <i class="icon pointer icon-arrow-l"></i>
      <i class="icon pointer icon-arrow-r"></i>
       <span :class="['img',{'img_active':index == count}]" v-for="(item,index) in urls">
           <preview-img :url="item" @select="select">
             <i class="loading_icon icon-load" slot="load"></i>
           </preview-img>
       </span>
    </div>
  </div>
</template>

<script>
import hwImg from "./components/hw-img.vue"
import hwLoad from "./components/hw-load.vue"
import previewImg from "./components/preview-img.vue"
export default {
  name: 'App',
  data(){return{
    imgs_srpead : false, //图片展开收起状态
    init_rotate : this.rotate, //初始旋转角度
    init_scale : this.scale, //初始缩放比例
    init_pre_img_css : this.preImgCss, //初始css样式
    w : '', //父容器宽度
    h : '', //父容易高度
    i_w : 0, //图片本身宽度
    i_h : 0, //图片本身高度
    sX : 0, //鼠标按下初始x坐标
    sY : 0, //鼠标按下初始y坐标
    i_top : 0, //初始化时图片top值
    i_left : 0, //初始化时图片left值
    initTop : 0, //每次移动的基础top值
    initLeft : 0, //每次移动的基础left值
    top : 0, //需要更改的预览图片的top值
    left : 0, //需要更改的预览图片的left值
    updatePos : false //更新图片位置信息
  }},
  components: {
      hwImg,hwLoad,previewImg
  },
  mounted(){
      this.imgs_srpead = true;
      this.w = parseInt(this.$refs.pre_img_content.offsetWidth);
      this.h = parseInt(this.$refs.pre_img_content.offsetHeight);
      setTimeout(()=>{
        this.$emit('update:url','https://www.hanyanjun.top/static/image/6.jpg');
      },1000);
  },
  watch:{
    i_top(v){
        this.top = v;
    },
    i_left(v){
      this.left = v;
    },
  },
  computed:{
    count(){
      let c = '-2';
      this.urls.forEach((v1,i)=>{
        if(v1 == this.url && this.url){
          c = i;
          return i;
        }
      })
      return c;
    },
    trans(){
      return {transform : `rotateZ(${this.rotate}deg) scale(${this.scale})`, marginTop : `${this.top}px` , marginLeft : `${this.left}px`, cursor: `${this.is_move ? 'move' : 'normal'}` };
    },
    is_move(){
      let m = false;
      if(this.rotate/90%2 == 0){
        m = this.i_w*this.scale > this.w || this.i_h*this.scale > this.h;
      }else{
        m = this.i_h*this.scale > this.w || this.i_w*this.scale > this.h;
      }
      m = true;
      this.$nextTick(()=>{
        if(m){
          this.$refs.pre_img_content.addEventListener('mousedown',this.touchstart,false);
        }else{
          this.top = this.i_top;
          this.left = this.i_left;
          this.$refs.pre_img_content.removeEventListener('mousedown',this.touchstart,false);
        }
      })
      return m;
    },
    maxT(){
//        可移动是在宽度或者高度溢出时
      let max = 0;
      if(this.rotate/90%2 == 0){
        max = (this.i_h*this.scale - this.h)/2 + this.h  - this.moveMinWidth ;
      }else{
        max = (this.i_w*this.scale - this.h)/2 + this.h  - this.moveMinWidth;
      }
      return max;
    },
    maxL(){
//        可移动是在宽度或者高度溢出时
      let max = 0;
      if(this.rotate/90%2 == 0){
        max = this.i_w*this.scale  + this.i_left - this.moveMinWidth;
      }else{
        max = (this.i_h*this.scale - this.w)/2 + this.w  - this.moveMinWidth;
      }
      console.log(max);
      return max;
    },
  },
  props:{
      title :{
          type : String,
          default : '图片预览'
      },
      urls:{
          type : Array,
          default : function () {
            return ['https://www.hanyanjun.top/static/image/2.jpg','https://www.hanyanjun.top/static/image/3.jpg','https://www.hanyanjun.top/static/image/4.jpg','https://www.hanyanjun.top/static/image/5.jpg','https://www.hanyanjun.top/static/image/6.jpg']
          }
      },
      url:{
        type : String,
        default : 'https://www.hanyanjun.top/static/image/6.jpg'
      },
      visible:{
          type : Boolean,
          default : true
      },
      rotate:{
          type: Number,
          default: 0
      },
      scale :{
          type : Number,
          default : 1
      },
      maxScale:{
          type : Number,
          default : 4
      },
      preImgCss:{
          type : Object,
          default : function () {
            return {}
          }
      },
      moveMinWidth : {
        type : Number,
        default : 30
      },
      moveMinHeight : {
        type : Number,
        default : 30
      },
  },
  methods:{
    select(v){
        this.$emit('update:url',v);
        this.$emit('select',v);
        this.url = v;
        this.init();
    },
    pre(v){
      if(this.count > 0){
        this.$emit('update:url',this.urls[this.count-1]);
        this.$emit('pre',this.urls[this.count-1]);
        this.url = this.urls[this.count-1];
        this.init();
      }else{
        this.$emit('suf','min');
      }
    },
    suf(v){
      if(this.count < this.urls.length - 1){
        this.$emit('update:url',this.urls[this.count+1]);
        this.$emit('suf',this.urls[this.count+1]);
        this.url = this.urls[this.count+1];
        this.init();
      }else{
        this.$emit('suf','max');
      }
    },
    bigger(){
      let s = this.scale;
      s += 0.3;
      if(s > this.maxScale){
        s = 4;
      }
      this.scale = s;
      this.$emit('update:scale',s);
    },
    smaller(){
      let s = this.scale;
      s -= 0.3;
      if(s < 0.3){
        s = 0.3;
      }
      this.scale = s;
      this.$emit('update:scale',s);

    },
    spin(){
      let r = this.rotate;
      r += 90;
      this.rotate = r;
      this.$emit('update:rotate',r);
    },
    init(){
      this.rotate = this.init_rotate;
      this.scale = this.init_scale;
      this.preImgCss = this.init_pre_img_css;
      this.$emit('update:rotate',this.init_rotate);
      this.$emit('update:preImgCss',this.init_pre_img_css);
      this.$emit('update:scale',this.init_scale);
    },
    touchstart(e){
      e = e || window.event;
      e.preventDefault();
      this.sY = parseInt(e.clientY);
      this.sX = parseInt(e.clientX);
      this.updatePos = true; //更新图片位置信息
      document.addEventListener('mousemove',this.touchmove,false);
      document.addEventListener('mouseup',this.tEventUp,false);
    },
    touchmove(e){
      e = e || window.event;
      e.preventDefault();
      let y = parseInt(e.clientY);
      let x = parseInt(e.clientX);
      let maxT = this.maxT;
      let maxL = this.maxL;
      console.log(this.initLeft);
      if(y <= this.sY){
        this.top = y - this.sY+ this.initTop  <=  - maxT + this.i_top?  - maxT + this.i_top  : y - this.sY + this.initTop ;
      }else{
        this.top =   y - this.sY + this.initTop >= maxT + this.i_top ?  maxT + this.i_top:  y - this.sY + this.initTop ;
      }
      if(x <= this.sX){
        this.left = x - this.sX + this.initLeft   <=  -maxL + this.initLeft ?  -maxL + this.initLeft :  x - this.sX + this.initLeft ;
      }else{
        this.left = x - this.sX + this.initLeft >=  maxL+ this.initLeft ?  maxL+ this.initLeft :  x - this.sX + this.initLeft ;
      }
    },
    tEventUp(e){
      document.removeEventListener('mousemove',this.touchmove,false);
    },
  }
}
</script>

<style lang="scss">
@import "./assets/common";
#hw-preview_img_wrap {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  position: fixed;
  height: 100%;
  width: 100%;
  left: 0;
  top:0;
  z-index: 100;
  background: rgba(0,0,0,0.8);
  .icon-close{
    position: absolute;
    font-size: 40px;
    top: 20px;
    right: 20px;
    color: white;
  }
  .title{
    position: absolute;
    left: 20px;
    top:20px;
    color: white;
  }
  .pre_img_content{
    width: 1000px;
    height: 600px;
    position: absolute;
    top: 50%;
    left: 50%;
    border: 1px solid rgba(255,255,255,0.3);
    margin-left: -500px;
    transform: translateY(-54%);
    .pre_img_con_cur{
      width: 100%;
      height: 600px;
      /*display: flex;*/
      /*justify-content: space-around;*/
      .icon{
        font-weight: bolder;
        color: #717171;
        line-height: 600px;
        font-size: 70px;
      }
      .icon:hover{
        color: white;
        cursor: pointer;
      }
    }
    .pre_img_cur{
      display: inline-block;
      width: 800px;
      height: 600px;
      overflow: hidden;
      border: 1px solid white;
      position: relative;
      vertical-align: middle;
      img{
        display: inline-block;
        transition:  transform 0.4s ease;
        transform-origin: 50% 50%;
      }
      .img_cur_img_w{
        width: 100%;
      }
      .img_cur_img_w{
        height: 100%;
      }
    }
    .pre_img_cur_move{
      cursor: move;
      border: 1px solid red;
    }
  }
  .pre_img_con_fn{
    width: 100%;
    height: 60px;
    /*border: 1px solid white;*/
    bottom: 0px;
    position: absolute;
    display: flex;
    .fn_module{
      color: white;
      display: flex;
      align-items: center;
      margin-left: 20px;
      font-size: 16px;
      line-height: 20px;
      .icon{
        margin-right: 3px;
        color: white;
        font-weight: bolder;
      }
    }
  }
  .pre_img_con_fn_active{
    bottom: 100px;
  }
  .pre_img_con_list{
    min-width: 100%;
    height: 100px;
    position: absolute;
    display: flex;
    flex-wrap: nowrap;
    bottom: 0;
    background: black;
    text-align: center;
    .img{
      display: inline-block;
      width: 100px;
      height: 80px;
      margin-left: 10px;
      margin-top: 10px;
      border: 1px solid rgba(255,255,255,0.3);
      position: relative;
      color: white;
    }
    .img_active{
      border: 1px solid #2178ef;
    }
    .icon{
      display: inline-block;
      height: 100px;
      line-height: 100px;
      font-size: 30px;
      position: fixed;
      color: white;
      bottom: 0;
      z-index: 4;
    }
    .icon:hover{
      background: rgba(255,255,255,0.3);
      color: white;
    }
    .icon-arrow-l{
      left: 0;
    }
    .icon-arrow-r{
      right: 0;
    }
  }
  .loading_icon{
    font-size: 40px;
    color: white;
    line-height: 60px;
    font-weight: normal;
    animation: rotate 2s ease infinite;
    transform-origin: 50% 50%;
  }
  @keyframes rotate {
    from {
      transform: rotate(0deg);
    }
    to {
      transform: rotate(360deg);
    }
  }
}
@font-face {
  font-family: 'icomoon';
  src:  url('../static/fonts/icomoon.eot?fda5jm');
  src:  url('../static/fonts/icomoon.eot?fda5jm#iefix') format('embedded-opentype'),
  url('../static/fonts/icomoon.ttf?fda5jm') format('truetype'),
  url('../static/fonts/icomoon.woff?fda5jm') format('woff'),
  url('../static/fonts/icomoon.svg?fda5jm#icomoon') format('svg');
  font-weight: normal;
  font-style: normal;
}

[class^="icon-"], [class*=" icon-"] {
  /* use !important to prevent issues with browser extensions that change fonts */
  font-family: 'icomoon' !important;
  speak: none;
  font-style: normal;
  font-weight: normal;
  font-variant: normal;
  text-transform: none;
  line-height: 1;

  /* Better Font Rendering =========== */
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.icon-load:before {
  content: "\e904";
}
.icon-load1:before {
  content: "\e912";
}
.icon-arrow-b:before {
  content: "\e900";
}
.icon-add:before {
  content: "\e901";
}
.icon-close:before {
  content: "\e902";
}
.icon-arrow-r:before {
  content: "\e903";
}
.icon-arrow-top:before {
  content: "\e90c";
}
.icon-arrow-down:before {
  content: "\e90d";
}
.icon-offline:before {
  content: "\e906";
}
.icon-arrow-t:before {
  content: "\e907";
}
.icon-arrow-l:before {
  content: "\e909";
}
.icon-scan:before {
  content: "\e90a";
}
.icon-play:before {
  content: "\e910";
}
.icon-scale-up:before {
  content: "\e905";
}
.icon-scale-down:before {
  content: "\e908";
}
.icon-all:before {
  content: "\e90e";
}
.icon-pause:before {
  content: "\e90b";
}
.icon-rotate:before {
  content: "\e90f";
}
.icon-init:before {
  content: "\e911";
}
</style>
