<script setup>
import {supabase} from '../supabase'
import {ref, onMounted} from 'vue'
import {TrashIcon} from '@heroicons/vue/24/outline'
import {CheckCircleIcon} from '@heroicons/vue/24/solid';
import {ArrowTrendingUpIcon} from '@heroicons/vue/24/solid';


const todos = ref([])
const addTodo = ref("")
const dropdown = ref(false)
const noText = ref(false)

const getData = async() => {
  const {data} = await supabase.from('ToDos').select();
  
  todos.value = data
}

const changeStatus = async(id, status) => {
  const {changeStatus} = await supabase.from('ToDos').update({Status: !status}).eq('id', id);
  getData()
}

const addToList = async() => {
  if(addTodo.value !== ""){
    noText.value = false
    const {addToList} = await supabase.from('ToDos').insert({WhatToDo: addTodo.value, Status: false})
    dropdown.value = false
    addTodo.value = ""
    getData()
  } else{
    noText.value = true
  }
}

const deleteTodo = async(id) => {
  const {deleteTodo} = await supabase.from('ToDos').delete().eq('id', id)
  getData()
}

onMounted(getData)

</script>
<template>
  <div class="mx-auto bg-transparent p-7 mt-8 max-w-xl w-full rounded-2xl text-center text-slate-800 border-2 border-slate-600 text-3xl font-bold">
    <span>To Do List</span>
  </div>
  <div class="m-auto bg-transparent p-8 mt-6 max-w-xl w-full rounded-2xl border border-slate-800">
    <div v-if="todos.length != 0" class="flex justify-center py-2 px-8 bg-transparent text-slate-900 font-bold rounded-xl text-lg">
      <span class="-ml-36 mr-44">Task</span>
      <span>Status</span>
    </div>
    <div v-for="list in todos" :class="`flex justify-center text-lg rounded-2xl p-3 mx-auto mt-4 items-center ${list.Status ? 'bg-slate-300' : 'bg-transparent'} border border-slate-800 text-slate-900 max-w-lg w-full`">
      <span class="w-40 -ml-8"> {{ list.WhatToDo }} </span>
      <button @click="changeStatus(list.id, list.Status)" class="mr-20 ml-16 text-slate-100 bg-transparent justify-center flex items-center rounded-lg w-10 h-10" v-if="list.Status">
        <CheckCircleIcon class="w-9 h-9 text-green-700"/>
      </button>
      <button @click="changeStatus(list.id, list.Status)" class="mr-20 ml-16 text-slate-900 bg-transparent rounded-lg w-10 h-10" v-else>
        <ArrowTrendingUpIcon class="w-9 h-9 text-slate-800"/>
      </button>
      <button @click="deleteTodo(list.id)" class="text-red-700 hover:text-red-600 hover:border-red-600 border-2 border-red-700 rounded-lg w-10 h-10 bg-transparent">
        <TrashIcon class="h-6 w-5 m-auto"/>
      </button>
    </div>

    <div class="mx-auto mt-6 text-center">
      <button @click="dropdown = !dropdown, addTodo='', noText=false" class="hover:bg-slate-200 bg-transparent text-slate-800 border-2 border-slate-600 font-bold max-w-xs w-full h-16 text-xl rounded-2xl">
        New
      </button>
      <input v-if="dropdown" v-on:keyup.enter="addToList" :class="`${noText ? 'bg-red-100 placeholder:text-red-400' : 'bg-transparent placeholder:text-slate-500'} border border-slate-500 p-3 w-64 mt-2 rounded-xl focus:bg-slate-200 focus:placeholder:text-slate-500 outline-none`" placeholder="What to do?" v-model="addTodo">
      <button v-if="dropdown" class="bg-transparent block w-24 h-10 text-slate-800 font-bold border border-slate-800 text-xl rounded-xl mx-auto mt-1 focus:bg-slate-300" @click="addToList">Add</button>
    </div>
    
  </div>

</template>

<style>
body{
  background-color: rgb(233, 235, 241);
}
</style>