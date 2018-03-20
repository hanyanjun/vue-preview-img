<template>
<div :class="['pre_img_cur']" ref="pre_img_content" @click.stop="select_img" >
  <img :src="url" alt=""  :style="[img_w_h,trans,preImgCss]" v-show="loaded && url"  ref="preview_img">
  <template v-if="!loaded">
    <hw-load v-show="!$slots.load"></hw-load>
    <div class="pre_img_init_load">
      <slot name="load"></slot>
    </div>
  </template>
</div>
</template>

<script>
import hwLoad from "./hw-load.vue"
export default{
    components :{hwLoad},
    data(){return{
        loaded :false,  //图片是否加载完毕
        img_w_h : '',
        w : '', //父容器宽度
        h : '', //父容易高度
        i_w : '', //图片本身宽度
        i_h : '', //图片本身高度
        initTop : 0, //初始的top值
        initLeft : 0, //初始的left值
        top : 0,
        left : 0
    }},
    props:{
        url : {
            type : String,
            default : ''  //显示的图片
        },
        load:{
            type : Boolean,
            default : true
        },
        scale:{
            type : Number,
            default : 1
        },
        rotate:{
            type : Number,
            default : 0
        },
        initWidth :{
           type : Number,
           default :0
        },
        initHeight:{
            type : Number,
            default : 0
        },
        updatePos:{
            type : Boolean,
            default : false
        },
        preImgCss:{
            type : Object,
            default : function () {
              return {};
            }
        }
    },
    computed:{
      trans(){
        return {transform : `rotateZ(${this.rotate}deg) scale(${this.scale})`, marginTop : `${this.top}px` , marginLeft : `${this.left}px` };
      },
    },
    methods:{
      init(v){
        this.loaded = false;
        setTimeout(()=>{
          let image = new Image();
          image.src =v;
          image.onload =  ()=> {
            this.w = parseInt(this.$refs.pre_img_content.offsetWidth);
            this.h = parseInt(this.$refs.pre_img_content.offsetHeight);
            let w = this.w;
            let h = this.h;
            let i_w = image.width;
            let i_h = image.height;
            let s = i_w/i_h;
            this.i_w = i_w;
            this.i_h = i_h;
            this.loaded = true;
            if(i_w >= w || i_h >= h){
              if(s >= 1){
                this.i_w = w;
                this.i_h = w/s;
                this.top = (h - w/s)/2;
                this.left = 0;
                this.img_w_h = {width:'100%'};
              }else{
                this.i_h = h;
                this.i_w = w*s;
                this.top = 0;
                this.left = (w - h*s)/2;
                this.img_w_h = {height:'100%'};
              }
            }else{
              this.img_w_h = {};
              this.top = (h - i_h)/2;
              this.left =(w - i_w)/2;
            }
            this.$emit('update:initWidth',this.i_w);
            this.$emit('update:initHeight',this.i_h);
            this.$emit('update:i_top',this.top);
            this.$emit('update:i_left',this.left);
          }
        },300);
      },
      select_img(){
        this.$emit('select',this.url);
      }
    },
    mounted(){
      this.init(this.url);
    },
    watch:{
        url(v,o){
            if(v){
                this.init(v);
            }
        },
        updatePos(v,o){
            if(v){
              let top = this.$refs.preview_img.offsetTop;
              let left = this.$refs.preview_img.offsetLeft;
              this.$emit('update:initTop',top);
              this.$emit('update:initLeft',left);
              this.$emit('update:updatePos',false);
            }
      }
    }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.pre_img_cur{
  position: relative;
  width: 100%;
  height: 100%;
  cursor: pointer;
  .pre_img_init_load{
    color: white;
    position: absolute;
    width: 60px;
    height: 60px;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
  }
  .mask{
    display: inline-block;
    width: 30px;
    height: 30px;
    background: black;
    right: 0;
    top: 0;
    position: absolute;
    z-index: 50;
  }
}
</style>
