<script setup>
import {supabase} from '../supabase'
import {ref, onMounted} from 'vue'
import {TrashIcon} from '@heroicons/vue/24/outline'
import {CheckCircleIcon} from '@heroicons/vue/24/outline';


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
    const {addToList} = await supabase.from('ToDos').insert({id: todos.value.length+1, WhatToDo: addTodo.value, Status: false})
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
  <div class="mx-auto bg-blue-700 p-10 mt-16 max-w-xl w-full h-32 rounded-2xl shadow-xl shadow-blue-800 text-center text-slate-200 text-3xl font-bold">
    <span>To Do List</span>
  </div>
  <div class="m-auto bg-blue-700 p-10 mt-12 max-w-xl w-full rounded-2xl">
    <div v-if="todos.length != 0" class="flex justify-center py-4 px-8 bg-blue-500 text-slate-100 font-bold rounded-xl text-lg">
      <span class="-ml-28 mr-44">Task</span>
      <span>Status</span>
    </div>
    <div v-for="list in todos" :class="`flex justify-center text-lg rounded-2xl p-6 mx-auto mt-8 ${list.Status ? 'bg-green-400 text-green-900' : 'bg-blue-300 text-blue-600'} max-w-lg w-full`">
      <span class="w-40"> {{ list.WhatToDo }} </span>
      <button @click="changeStatus(list.id, list.Status)" :class="`mr-20 ml-14 text-slate-100 p-2 rounded-lg w-10 h-10 ${list.Status ? 'bg-green-700' : 'bg-blue-600'}`" v-if="list.Status">
        <CheckCircleIcon class="w-6 h-6 text-slate-100"/>
      </button>
      <button @click="changeStatus(list.id, list.Status)" :class="`mr-8 ml-4 text-slate-100 p-2 rounded-lg w-32 h-10 ${list.Status ? 'bg-green-700' : 'bg-blue-600'}`" v-else>
        In process
      </button>
      <button @click="deleteTodo(list.id)" class="text-slate-100 p-2 rounded-lg w-10 h-10 bg-red-700">
        <TrashIcon class="h-6 w-5 text-slate-200 m-auto"/>
      </button>
    </div>

    <div class="mx-auto mt-9 text-center">
      <button @click="dropdown = !dropdown, addTodo=''" class="bg-transparent text-slate-200 border-2 border-slate-300 font-bold max-w-xs w-full h-16 text-xl rounded-2xl">
        New
      </button>
      <input v-if="dropdown" :class="`${noText ? 'bg-red-300 placeholder:text-red-700' : 'bg-blue-100 placeholder:text-slate-500'} p-3 w-64 mt-4 rounded-xl focus:bg-blue-200 focus:placeholder:text-slate-500 outline-none`" placeholder="What to do?" v-model="addTodo">
      <button v-if="dropdown" class="bg-slate-200 block w-32 h-10 text-blue-600 font-bold text-xl rounded-xl mx-auto mt-2 focus:bg-blue-300" @click="addToList">Add</button>
    </div>
    
  </div>

</template>
