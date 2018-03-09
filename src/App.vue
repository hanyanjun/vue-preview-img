<template>
  <div id="hw-preview_img_wrap" v-show="visible || img">
    <h6 class="title">{{title}}</h6>
    <i class="icon-close pointer"></i>
    <div class="pre_img_content">
      <div class="pre_img_con_cur">
        <i class="icon icon-arrow-l"></i>
        <div class="pre_img_cur">

        </div>
        <i class="icon icon-arrow-r"></i>
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
       </span><span class="fn_module pointer">
           <i class="icon icon-scale-up"></i>
            放大
       </span><span class="fn_module pointer">
           <i class="icon icon-scale-down"></i>
            缩小
       </span><span class="fn_module pointer">
           <i class="icon icon-rotate"></i>
            旋转
       </span><span class="fn_module pointer">
           <i class="icon icon-init"></i>
            复位
       </span>
    </div>
    <div class="pre_img_con_list" v-show="imgs_srpead">
      <i class="icon pointer icon-arrow-l"></i>
      <i class="icon pointer icon-arrow-r"></i>
       <span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span><span class="img">

       </span>
    </div>
  </div>
</template>

<script>
import hwImg from "./components/hw-img.vue"
export default {
  name: 'App',
  data(){return{
    imgs_srpead : false, //图片展开收起状态
  }},
  components: {
      hwImg
  },
  props:{
      title :{
          type : String,
          default : '图片预览'
      },
      imgs:{
          type : Array,
          default : function () {
            return ['https://www.hanyanjun.top/static/image/2.jpg','https://www.hanyanjun.top/static/image/3.jpg','https://www.hanyanjun.top/static/image/4.jpg','https://www.hanyanjun.top/static/image/5.jpg','https://www.hanyanjun.top/static/image/6.jpg']
          }
      },
      img:{
        type : String,
        default : ''
      },
      visible:{
          type : Boolean,
          default : true
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
    top: 50%;
    left: 50%;
    border: 1px solid white;
    margin-left: -500px;
    transform: translateY(-54%);
    .pre_img_con_cur{
      width: 100%;
      height: 600px;
      display: flex;
      justify-content: space-around;
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
      width: 800px;
      height: 600px;
      overflow: hidden;
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
      .move{
        cursor: move;
      }
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
    height: 100px;
    /*border: 1px solid white;*/
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
      border: 1px solid white;
    }
    .icon{
      display: inline-block;
      height: 100px;
      line-height: 100px;
      font-size: 30px;
      position: fixed;
      color: white;
      bottom: 0;
    }
    .icon-arrow-l{
      left: 10px;
    }
    .icon-arrow-r{
      right: 10px;
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
