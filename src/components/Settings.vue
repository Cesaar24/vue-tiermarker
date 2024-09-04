<template>
<div class="settings">
  <div class="setting" @click="settings"></div>
  <div class="control">
    <div class="control-up" @click="control(true)"></div>
    <div class="control-botton" @click="control(false)"></div>
  </div>
</div>
</template>
<script setup>
/* import { ref } from 'vue'; */

const emit = defineEmits(['move','show-dialog'])
const control = (up) => {
  if(up){
    console.log("move up")
    emit('move',{up:true})
  }else{
    console.log("move down")
    emit('move',{up:false})
  }
  
}
const settings = () => {
 console.log("settings")
 emit('show-dialog')

}
</script>
<style>
.settings{
  width: 120px;
  max-width: 120px;
  display: flex;
  background: #111;
  /* height: 100%; */
}
.setting{
  width: 50px;
  height: 100%;
  background: url('./settings.png') center no-repeat;
}
.setting:hover{
  opacity: .5;
  cursor: pointer;
}
.control{
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.control-up{
  width: 50px;
  height: 50px;
  background: url('./arrowup.png') center no-repeat;
}
.control-up:hover{
  opacity: .5;
  cursor: pointer;
}
.control-botton{
  width: 50px;
  height: 50px;
  background: url('./arrowdown.png') center no-repeat;
}
.control-botton:hover{
  opacity: .5;
  cursor: pointer;
}
</style>