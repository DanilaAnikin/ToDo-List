<script setup>
import {supabase} from '../supabase'
import {ref, onMounted} from 'vue'

const allData = ref([])
const finish = ref()

const getData = async() => {
  const {data} = await supabase.from('ToDos').select();
  
  allData.value = data
}

const changeStatus = async(id, status) => {
  const {changeStatus} = await supabase.from('ToDos').update({Status: !status}).eq('id', id);
  const {data} = await supabase.from('ToDos').select();
  allData.value = data
}

onMounted(getData)

</script>

<template>
  <div class="mx-auto bg-blue-700 p-10 mt-16 max-w-xl w-full h-32 rounded-2xl shadow-xl shadow-blue-800 text-center text-slate-200 text-3xl font-bold">
    <span>To Do List</span>
  </div>
  <div class="m-auto bg-blue-700 p-10 mt-12 max-w-xl w-full rounded-2xl shadow-2xl shadow-blue-600">
    <div class="flex justify-between py-4 px-8 bg-blue-500 text-slate-100 font-bold rounded-xl text-lg shadow-xl shadow-blue-700">
      <span>Task</span>
      <span>Until when</span>
      <span>Status</span>
    </div>
    <div v-for="list in allData" :class="`flex justify-between text-lg shadow-xl shadow-blue-900 rounded-2xl p-6 mx-auto mt-8 ${list.Status ? 'bg-green-400 text-green-900' : 'bg-blue-300 text-blue-600'} max-w-lg w-full h-20`">
      <span> {{ list.WhatToDo }} </span>
      <span> {{ list.UntilWhen }} </span>
      <button @click="changeStatus(list.id, list.Status)" :class="`text-slate-100 p-2 rounded-lg w-20 h-10 -mt-1 ${list.Status ? 'bg-green-700' : 'bg-blue-600'}`" v-if="list.Status">Finished</button>
      <button @click="changeStatus(list.id, list.Status)" :class="`text-slate-100 p-2 rounded-lg w-20 h-10 -mt-1 ${list.Status ? 'bg-green-700' : 'bg-blue-600'}`" v-else>Finish</button>
    </div>
  </div>
</template>
