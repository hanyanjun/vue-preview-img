<template>
  <div id="app">
    <!--<img src="./assets/logo.png">-->
    <img :src="`https://www.hanyanjun.top/static/image/${item}.jpg`" alt="" v-for="item in  imgs">
    <previewImg></previewImg>
  </div>
</template>

<script>
import previewImg from './components/preview-img'

export default {
  name: 'App',
  data(){return{
     imgs : ['2','3','4','5','6']
  }},
  components: {
    previewImg
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  img{
    display: inline-block;
    width: 100px;
    height: 100px;
    margin-right: 30px;
    cursor: pointer;
  }
}
</style>
