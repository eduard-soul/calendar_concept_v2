<template>
  <div id="tasks">
    <div v-for="(task, n) in tasks_array" v-bind:key="n" :id="'task'+ n" class="task">
      {{task.name}} {{n}}
      {{task.duration}}
      <button @click="deleteTask(n)">X</button>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { reactive } from 'vue';
  
  interface Task_object {
    name: string,
    duration: number,
  }
  let tasks_array:Array<Task_object> = reactive([]);

  let task = {name: "Task", duration: 120};

  tasks_array.push(task);

  function deleteTask(n: number) {
    tasks_array.splice(n, 1);
  }

</script>

<style lang="scss" scoped>
  #tasks {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: yellow;
  }
  .task {
    width: 90%; 
    height: auto;
    background-color: greenyellow;
  }
</style>
