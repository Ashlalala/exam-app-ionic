<template>
  <base-layout>

      

<div id="content">
  <div id="div1" class="mydiv">ddd</div>
  <div id="div2" class="mydiv">ttt</div>
  <div id="line"></div>
</div>
  
  </base-layout>
</template>

<script setup>
import {useMainStore} from '@/stores/MainStore'
import {onMounted} from 'vue'

const main = useMainStore()

function adjustLine(from, to, line){

  var fT = from.offsetTop  + from.offsetHeight/2;
  var tT = to.offsetTop    + to.offsetHeight/2;
  var fL = from.offsetLeft + from.offsetWidth/2;
  var tL = to.offsetLeft   + to.offsetWidth/2;

  var CA   = Math.abs(tT - fT);
  var CO   = Math.abs(tL - fL);
  var H    = Math.sqrt(CA*CA + CO*CO);
  var ANG  = 180 / Math.PI * Math.acos( CA/H );

  if(tT > fT){
      var top  = (tT-fT)/2 + fT;
  }else{
      var top  = (fT-tT)/2 + tT;
  }
  if(tL > fL){
      var left = (tL-fL)/2 + fL;
  }else{
      var left = (fL-tL)/2 + tL;
  }

  if(( fT < tT && fL < tL) || ( tT < fT && tL < fL) || (fT > tT && fL > tL) || (tT > fT && tL > fL)){
    ANG *= -1;
  }
  top-= H/2;

  line.style["-webkit-transform"] = 'rotate('+ ANG +'deg)';
  line.style["-moz-transform"] = 'rotate('+ ANG +'deg)';
  line.style["-ms-transform"] = 'rotate('+ ANG +'deg)';
  line.style["-o-transform"] = 'rotate('+ ANG +'deg)';
  line.style["-transform"] = 'rotate('+ ANG +'deg)';
  line.style.top    = top+'px';
  line.style.left   = left+'px';
  line.style.height = H + 'px';
}

onMounted(() => {
  adjustLine(
  document.getElementById('div1'), 
  document.getElementById('div2'),
  document.getElementById('line')
);
})



</script>





<style>
#content{
  position:relative;
}
.mydiv{
  border:1px solid #368ABB;
  background-color:#43A4DC;
  position:absolute;
}
.mydiv:after{
  content:no-close-quote;
  position:absolute;
  top:50%;
  left:50%;
  background-color:black;
  width:4px;
  height:4px;
  border-radius:50%;
  margin-left:-2px;
  margin-top:-2px;
}
#div1{
  left:200px;
  top:200px;
  width:50px;
  height:50px;
}
#div2{
  left:20px;
  top:20px;
  width:50px;
  height:40px;
}
#line{
  position:absolute;
  width:1px;
  background-color:red;
}  
</style>