<template>
  <div id="hw-preview_load">
    <span class="stick"></span>
    <span class="stick"></span>
    <span class="stick"></span>
    <span class="stick"></span>
    <span class="stick"></span>
    <span class="stick"></span>
    <h1>Loading...</h1>
  </div>
</template>

<script>
export default{

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
$text_color: white;
$stick_color: white; //Changes the colour of all sticks
$stick_length: 30px; //Changes length of all sticks
$stick_distance: -8px; //Changes distance between sticks (will need editing after 30px)
  @import url(https://fonts.googleapis.com/css?family=Lato:100,300,700);
  #hw-preview_load {
    position: absolute;
    top: 50%;
    left: 50%;
    /*border: 1px solid greenyellow;*/
    text-align: center;
    transform: translate(-50%,-50%);
    h1 {
      font-family: 'Lato';
      color:$text_color;
      text-transform: uppercase;
      font-size:1em;
      letter-spacing:1.5px;
      text-align:center;
      width:155px;
      margin: 20px auto 0;
      -webkit-animation: fade 2s infinite;
      -moz-animation: fade 2s infinite;
    }
    .stick {
      width: $stick_length;
      height:3px;
      background:$stick_color;
      display:inline-block;
      margin-left:$stick_distance;
    }

    @-webkit-keyframes rise {
      50% { transform:rotate(30deg);
        -ms-transform:rotate(30deg); /* IE 9 */
        -webkit-transform:rotate(30deg);
        -moz-transform:rotate(30deg);
      ;}
    }
    @-moz-keyframes rise {
      50% { transform:rotate(30deg);
        -ms-transform:rotate(30deg); /* IE 9 */
        -webkit-transform:rotate(30deg);
        -moz-transform:rotate(30deg);
      }
    }
    @-o-keyframes rise {
      50% { transform:rotate(30deg);
        -ms-transform:rotate(30deg); /* IE 9 */
        -webkit-transform:rotate(30deg);
        -moz-transform:rotate(30deg);
      }}

    @keyframes rise {
      50% { transform:rotate(30deg);
        -ms-transform:rotate(30deg); /* IE 9 */
        -webkit-transform:rotate(30deg);
        -moz-transform:rotate(30deg);
      }
    }

    @-webkit-keyframes fall {
      50% { transform:rotate(-30deg);
        -ms-transform:rotate(-30deg); /* IE 9 */
        -webkit-transform:rotate(-30deg);
        -moz-transform:rotate(30deg);
      }
    }
    @-moz-keyframes fall {
      50% { transform:rotate(-30deg);
        -ms-transform:rotate(-30deg); /* IE 9 */
        -webkit-transform:rotate(-30deg);
        -moz-transform:rotate(-30deg);

      }
    }
    @-o-keyframes fall {
      50% { transform:rotate(-30deg);
        -ms-transform:rotate(-30deg); /* IE 9 */
        -webkit-transform:rotate(-30deg);
        -moz-transform:rotate(30deg);

      }}
    @keyframes fall {
      50% { transform:rotate(-30deg);
        -ms-transform:rotate(-30deg); /* IE 9 */
        -webkit-transform:rotate(-30deg);
        -moz-transform:rotate(30deg);

      }
    }
    @-webkit-keyframes fade {
      50%{opacity:0.5}
      100%{opacity:1}}
    @-moz-keyframes fade {
      50%{opacity:0.5}
      100%{opacity:1}
    }
    @-o-keyframes fade {
      50%{opacity:0.5}
      100%{opacity:1}}
    @keyframes fade {
      50%{opacity:0.5}
      100%{opacity:1}
    }
    .stick:nth-child(n) {
      transform:rotate(30deg);
      -ms-transform:rotate(30deg); /* IE 9 */
      -webkit-transform:rotate(30deg); /* Safari and Chrome */
      -moz-transform:rotate(30deg);
      -webkit-animation: fall 2s infinite;
      -moz-animation: fall 2s infinite;

    }
    .stick:nth-child(2n) {
      transform:rotate(-30deg);
      -ms-transform:rotate(-30deg); /* IE 9 */
      -webkit-transform:rotate(-30deg); /* Safari and Chrome */
      -moz-transform:rotate(-30deg);
      -webkit-animation: rise 2s infinite;
      -moz-animation: rise 2s infinite;

    }
  }


</style>
