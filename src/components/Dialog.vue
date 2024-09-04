<template>
<div class="dialog">
  <div class="close" @click="emit('close')"></div>
  <div class="color-select">
    <span v-for="color in colors" :key="color" ref="spanColor" :id="color" @click="selected = color"></span>
  </div>
  <input class="input-text" type="text" v-model="text" @focusout="emit('update-item',{
    color:selected,
    name:text,
    index:item.index,
  })">
</div>
</template>
<script setup>
import { nextTick, onMounted, ref, watch} from 'vue';

const props = defineProps({
  item: {
    type: Object,
  }
})
const colors = ref([
  '#FF7F7F',
  '#FFBF7F',
  '#FFDF7F',
  '#FFFF7F',
  '#BFFF7F',
  '#7FFF7F',
  '#7FFFFF',
  '#7FBFFF',
  '#7F7FFF',
  '#FF7FFF',
  '#BF7FBF',
  '#3B3B3B',
  '#858585',
  '#CFCFCF',
  '#F7F7F7',
])
const spanColor = ref()
const selected = ref('')
const text = ref('')

const emit = defineEmits(['update-item','close'])

onMounted(() => {
  nextTick(()=>{
    colors.value.forEach((element,i) => {
      spanColor.value[i].style.background = element
    });

    selected.value = props.item.color
    text.value = props.item.name
  })
})
watch(selected,(newVal,oldVal) => {
  const dataSend = {
    color:selected.value,
    name:text.value,
    index:props.item.index,
  }
  spanColor.value.forEach((element) => {
    if(element.id == newVal ){
      element.classList.add('selected')
    }
    if(oldVal && (oldVal == element.id)){
      element.classList.remove('selected')
    }
  });
  if(oldVal){
    emit('update-item',dataSend)
  }
  
})


</script>
<style scoped>
.dialog{
  width: 700px;
  height: 400px;
  position: absolute;
  background: #fff;
  top: 0;
  bottom: 0;
  left: 0; 
  right: 0; 
  margin: auto auto; 
}

.color-select{
  margin:0 4px;
  margin-top:20px;
}
.color-select span{
  width:30px;height:30px;
  margin:3px;
  display:inline-block;
  transition:box-shadow .1s;
  cursor:pointer;
  border-radius:50%;
  border:2px solid #fff;
}
.color-select span.selected{
  border:solid 2px #000;
}
.input-text{
  text-align: center;
  border:1px solid #efefef;
  font-size:16px;
  margin:20px 0;
  box-sizing:border-box;
  padding:10px;
  width: 400px;
  height: 30px;
}

.close{
  margin-left: auto;
  width: 50px;
  height: 50px;
  background: url('./close.png') center no-repeat;
}
.close:hover{
  opacity: .6;
  cursor: pointer;
}

</style>
    