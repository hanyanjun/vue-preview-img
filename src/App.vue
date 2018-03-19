<template>
  <div id="hw-preview_img_wrap">
    <h6 class="title">{{title}}{{count+1}}/{{len}}</h6>
    <i class="icon-close pointer" @click.stop="close"></i>
    <div class="pre_img_content">
        <i class="icon icon-arrow-l" @click.stop="pre"></i>
        <div :class="['pre_img_cur_wrap',{'pre_img_cur_move':is_move}]"  ref="pre_img_content">
           <preview-img
             :url="url"
             :scale="scale"
             :rotate="rotate"
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
    <div class="imgs_info"></div>
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
      <span class="fn_module pointer" @click.stop="autoPlay">
          <template v-if="is_play">
           <i class="icon icon-pause"></i>
             暂停播放
          </template>
          <template v-if="!is_play">
           <i class="icon icon-play"></i>
             自动播放
          </template>
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
    <div class="pre_img_con_list" v-show="imgs_srpead" :style="listStyle">
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
    mX : 0,
    mY : 0,
    i_top : 0, //初始化时图片top值
    i_left : 0, //初始化时图片left值
    initTop : 0, //每次移动的基础top值
    initLeft : 0, //每次移动的基础left值
    top : 0, //需要更改的预览图片的top值
    left : 0, //需要更改的预览图片的left值
    updatePos : false, //更新图片位置信息
    is_play : false,//是否自动播放
    play_interval : '', //播放的定时器
    listStyle : {},
    initCount : 0
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
//      m = true;
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
        max = this.h + this.i_h*(this.scale-1)/2 - this.moveMinWidth ;
      }else{
        max = this.h  - this.i_h/2 + this.i_w*this.scale/2   - this.moveMinWidth;
      }
      return max;
    },
    maxL(){
//        可移动是在宽度或者高度溢出时
      let max = 0;
      if(this.rotate/90%2 == 0){
        max = (this.i_w*this.scale +  this.w)/2  - this.moveMinWidth;
      }else{
        max = (this.i_w+ this.i_h*this.scale)/2  - this.moveMinWidth;
      }
      return max;
    },
    len(){
        return this.urls.length;
    }
  },
  props:{
      title :{
          type : String,
          default : '图片预览'
      },
      urls:{
          type : Array,
          default : function () {
            return ['https://www.hanyanjun.top/static/image/2.jpg','https://www.hanyanjun.top/static/image/3.jpg','https://www.hanyanjun.top/static/image/4.jpg','https://www.hanyanjun.top/static/image/5.jpg','https://www.hanyanjun.top/static/image/6.jpg','https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1521460527371&di=45d13be1e15090e20d1bfb372bc404a1&imgtype=0&src=http%3A%2F%2Fimages2015.cnblogs.com%2Fblog%2F603812%2F201604%2F603812-20160423232657101-1637286243.png  ']
          }
      },
      url:{
        type : String,
        default : 'https://www.hanyanjun.top/static/image/3.jpg'
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
      period : {
         type : Number,
         default : 3000
      },
      minScale : {
          type : Number,
          default : 0.3
      },
      scaleDis:{
          type : Number,
          default : 0.3
      }
  },
  methods:{
    select(v){
        this.$emit('update:url',v);
        this.$emit('select',v);
        this.url = v;
        this.initCount = this.count;
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
      if(this.count < this.len - 1){
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
      s += this.scaleDis;
      if(s > this.maxScale){
        s = this.maxScale;
      }
      this.scale = s;
      this.$emit('update:scale',s);
    },
    smaller(){
      let s = this.scale;
      s -= this.scaleDis;
      if(s < this.minScale){
        s = this.minScale;
      }
      if(s <= 1){
        this.init();
      }
      this.scale = s;
      this.$emit('update:scale',s);
    },
    init_pos(t1,t2,direction){
        if(direction == 'y'){
          if(t1 <= t2){
            this.top = t1 - t2+ this.initTop  <=  - this.maxT + this.h - this.i_h  ?  - this.maxT + this.h - this.i_h     : t1 - t2 + this.initTop ;
          }else{
            this.top =  t1 - t2 + this.initTop >= this.maxT   ?  this.maxT   :  t1 - t2 + this.initTop ;
          }
        }else{
          if(t1 <= t2){
//            con
            this.left = t1 - t2 + this.initLeft   <=  - this.maxL  ?  - this.maxL   :  t1 - t2 + this.initLeft ;
          }else{
            this.left = t1 - t2 + this.initLeft >=  this.maxL ?  this.maxL :  t1 - t2 + this.initLeft ;
          }
        }
    },
    spin(){
      let r = this.rotate;
      r += 90;
      if(r == 360){
          r = 0;
      }
      this.rotate = r;
      this.init_pos(0,0,'y');
      this.init_pos(0,0,'x');
      this.$emit('update:rotate',r);
    },
    init(){
      this.top = this.i_top;
      this.left = this.i_left;
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
      this.init_pos(y,this.sY,'y');
      this.init_pos(x,this.sX,'x');
    },
    tEventUp(e){
      document.removeEventListener('mousemove',this.touchmove,false);
    },
    autoPlay(){
      this.is_play = !this.is_play;
      this.initCount = this.count;
      if(this.is_play){
        this.play_interval = setInterval(()=>{
            if(this.count +1 >= this.len){
                this.url = this.urls[0];
                this.$emit('update:url',this.urls[0]);
            }else{
              this.suf();
            }
            this.listStyle = {'marginLeft' : `-${(this.count - this.initCount)*120}px`}
        },this.period);
      }else{
          clearInterval(this.play_interval);
      }
    },
    close(){
        this.url = '';
        this.$emit('update:url','');
    }
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
    text-align: left;
    top: 50%;
    left: 50%;
    border: 1px solid rgba(255,255,255,0.3);
    transform: translateY(-54%);
    margin-left: -500px;
    display: flex;
    flex-wrap: nowrap;
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
    .pre_img_cur_wrap{
      width: 800px;
      height: 600px;
      overflow: hidden;
      position: relative;
      margin: 0 auto;
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
