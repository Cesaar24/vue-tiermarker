<template>
  <div class="items"
    @dragleave.prevent="handleDrag($event)"
    @dragover.prevent="handleDrag($event)"
    @drop="handleDrop"
  >
    <div v-for="(item,i) in items" :id="i" ref="divItems" :key="item" class="item" draggable="true"
      @dragstart="handleDrag($event,{...item,index:i})"
    >
      <img :src="item.url" :alt="i">
    </div>
  </div>


</template>
<script setup>
import { ref ,onMounted } from 'vue'
const numDefault = 6

const items = ref([])
const divItems = ref([])
const emit = defineEmits(['delete-item'])

onMounted(() => {
  
  for (let index = 0; index <= numDefault ; index++) {
    items.value.push({
      url:`/icons/images (${index}).png` 
    })
  }
})

const handleDrag = (e,item) => {
  const el = e.target
  if (e.type === "dragover") {
    el.classList.add('dragging')
  }
  if(e.type === "dragleave"){
    el.classList.remove('dragging')
  }
  if (e.type === "dragstart") {
    const objData = { ...item }
    e.dataTransfer.setData('text/plain', JSON.stringify(objData))
  }
}
const handleDrop = (e) => {
  const el = e.target
  el.classList.remove('dragging')
  const data = JSON.parse(e.dataTransfer.getData('text/plain'))
  const afterElement = getDrafAfterElement(e.target)
  if(afterElement?.id){
    const indexStart = data.index
    const indexDrop = afterElement.id
    if(typeof data.index == 'undefined'){
      /* console.log("caso 1",data) */
      items.value.splice(indexDrop,0,{url:data.url})
      emit("delete-item",{...data})
    }else{
      /* console.log("caso 2",data) */
      if(indexStart === indexDrop ) return 
      let aux = items.value[indexStart]
      items.value[indexStart] = items.value[indexDrop]
      items.value[indexDrop] = aux
    }
  }else{  
    
    if(typeof data.index == 'undefined'){
      /* console.log("caso 3",data) */
      items.value.push({url:data.url})
      emit("delete-item",{...data})
    }else{
      /* console.log("caso 4",data) */
      items.value.push({url:data.url})
      items.value.splice(data.index,1)
    }
  }
}
const getDrafAfterElement = (target) => {
  const divs = divItems.value
  if (!divs) {
    return null
  }
  return divs.filter((e) => {
    return e.contains(target)
  })[0]
}
const deleteItem = (i) =>{
  items.value.splice(i,1)
}
defineExpose({ deleteItem })


</script>
<style scoped>

.items{
  display: flex;
  flex-flow: wrap;
  margin-top: 12px;
  border: 2px solid rgba(0, 0, 0, 0.8);
  background: #ffffff23;
  height: 96px;
  transition: all .3s ease;
}
.item{
  width: 90px;
  height: 100%;
}
.item:hover{
  cursor:move;
}
img{
  width: 100%;
  height: 100%;
}
.dragging{
  background: #3561d8;

}

</style>
  