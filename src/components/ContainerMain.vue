<template>
  <div class="flex-container">
    <div v-for="(type,i) in types" :key="i" class="flex-column-items">
      <Settings 
        @move="moveUp($event,i)"
        @show-dialog="setItem($event,type,i)"
      />
      <div class="itemBox" ref="DivsType"
       contenteditable="true"
       spellcheck="false"
       @input="onInput($event,i)"
      >
        <div class="item-text">
          <span>{{type.name}}</span>
        </div>
      </div>

      <div class="items"
        @dragleave.prevent="handleDrag($event)"
        @dragover.prevent="handleDrag($event)"
        @drop="handleDrop($event,i)"
      >
        <div v-for="(item,j) in type.items" :key="j" :id="j" class="item"
          ref="divItems"
          draggable="true"
          @dragstart="handleDrag($event,{...item,indexToDelete:j,indexTypeStart:i})"
        >
          <img :src="item.url" :alt="item">
        </div>
      </div>
    </div>
  </div>
  <Items 
    ref="componentItems"
    @delete-item="deleteItem($event)"
  />
  <Dialog
    v-if="showDialog"
    :item = "ItemEdit"
    @update-item="updateItem($event)"
    @close="showDialog = !showDialog"
  />
</template>
<script setup>
import Items from  './Items.vue'
import Dialog from  './Dialog.vue'
import Settings from  './Settings.vue'

import { nextTick, onMounted, ref, watch, } from 'vue';

const types = ref([])
const showDialog = ref(false)
const ItemEdit = ref({})

const DivsType = ref([])
const divItems = ref([])
const componentItems = ref()
const defaultTypes = ['s','a','b','c','d','e']
const defaulColors = ['#FF7F7F','#FFBF7F','#FFDF7F','#FFFF7F','#BFFF7F','#7FFF7F']
onMounted(() => {
  for (let index = 0; index < 6; index++) {
    const data = {
      name:defaultTypes[index],
      color:defaulColors[index],
      items:[]
    }
    types.value.push(data)
  }

})

watch(types,(newVal) => {
  nextTick(()=>{
    types.value.forEach((element,i) => {
      DivsType.value[i].style.background = element.color
    });    
  })
},{deep:true,})

const onInput = (e,i) => {
  types.value[i].name = e.target.innerText
}
const updateItem = (e) => {
  /* console.log("updateItem:",e) */
  types.value[e.index].color =  e.color
  types.value[e.index].name =  e.name
}
const setItem = (e,type,i) => {
  ItemEdit.value = {...type,index:i}
  showDialog.value = !showDialog.value
}
const moveUp = (e,typeIndex) => {
  /* console.log(e,typeIndex) */
  if((typeIndex == 0 && e.up) || ((types.value.length - 1) === typeIndex && !e.up) ) return
  const indexMove = e.up ? typeIndex - 1  : typeIndex + 1 
  
  /* swap */
  let aux = types.value[typeIndex]
  types.value[typeIndex] = types.value[indexMove]
  types.value[indexMove] = aux


}
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
const handleDrop = (e,indexTypeDrop) => {
  const el = e.target
  el.classList.remove('dragging')
  
  const data = JSON.parse(e.dataTransfer.getData('text/plain'))
  const afterElement = getDrafAfterElement(e.target)

  if(afterElement?.id){
    if(typeof data.indexToDelete !== 'undefined'){
      /* caso 1 */
      const indexStart = data.indexToDelete
      const indexDrop = afterElement.id
      if(indexStart === indexDrop ) return
      if(indexTypeDrop !== data.indexTypeStart){
        /* console.log("caso 1:",data) */
        types.value[indexTypeDrop].items.splice(indexDrop,0,{url:data.url})
        types.value[data.indexTypeStart].items.splice(indexStart,1)
      }else{
        /* console.log("caso 2:",data) */
        let aux = types.value[indexTypeDrop].items[indexStart]
        types.value[indexTypeDrop].items[indexStart] = types.value[indexTypeDrop].items[indexDrop]
        types.value[indexTypeDrop].items[indexDrop] = aux
      }
    }else{
      /* caso 2 */
      /* console.log("caso 3",data) */
      const indexDrop = afterElement.id
      types.value[indexTypeDrop].items.splice(indexDrop,0,{url:data.url})
      componentItems.value.deleteItem(data.index)
    }
  }else{
    if(typeof data.indexToDelete !== 'undefined'){
      /* caso 3 */
      /* console.log("caso 4",data) */
      types.value[indexTypeDrop].items.push({url:data.url})
      types.value[data.indexTypeStart].items.splice(data.indexToDelete,1)
    }else{
      /* caso 4 */
      /* console.log("caso 5",data) */
      types.value[indexTypeDrop].items.push({url:data.url})
      componentItems.value.deleteItem(data.index)
    }
  }
}
const deleteItem = (data) => { 
  types.value[data.indexTypeStart].items.splice(data.indexToDelete,1)
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
</script>
<style scoped>
.flex-container {
  display: flex;
  flex-direction: column;
  margin-top: 50px;
  background: #ffffff23;
}
.flex-column-items {
  display: flex;
  border: 2px solid rgba(0, 0, 0, 0.8);
  font-size: 20px;
}

.itemBox{
  width: 120px;
  max-width: 120px;
  align-content: center;
  
}
.item-text{
  width:100px;
  display:flex;
  align-items:center;
  align-items:center;
  justify-content:center;
}
.item-text span{
  color:#333;
  overflow:hidden;
  text-align:center;
  margin:5px;
  -webkit-text-stroke: medium;
  font-size: medium;
}

.items{
  display: flex;
  flex-flow: wrap;
  width: 100%;
  transition: all .3s ease;  
}
.dragging{
  background: #3561d8;
}
.item:hover{
  cursor:move;
}
.item{
  width: 90px;
  height: 100px;
}
img{
  width: 100%;
  height: 100px;
}


</style>
