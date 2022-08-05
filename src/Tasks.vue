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
  import { inject, onMounted, onUpdated, reactive, watch} from 'vue';
  
  interface Task_object {
    name: string,
    duration: number,
  }
  let tasks_array:Array<Task_object> = reactive([]);
  let task_provided = reactive({name: "", duration: 0});

  function deleteTask(n: number) {
    tasks_array.splice(n, 1);
  }

  task_provided = inject('task_provide');

  watch(task_provided, () => {
    let task:Task_object = {name: "", duration: 0};

    task.name = task_provided.name;
    task.duration = task_provided.duration;
    tasks_array.push(task);
  })
  // setInterval(() => {
  //   console.log(task_provided);
  // }, 1000)

  onMounted(() => {
  })

  onUpdated(() => {
    console.log("Updated");
    console.log(task_provided);
  })
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
