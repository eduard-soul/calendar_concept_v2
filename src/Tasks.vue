<template>
  <div id="tasks">
    <div
      v-for="(task, n) in tasks_array"
      v-bind:key="n"
      :id="'task' + n"
      class="task"
      @mousedown="taskDrag(n, true), task_index = n"
      @mouseup="taskDrag(n, false), task_index = n"
    >
      {{ task.name }} {{ n }}
      {{ task.duration }}
      <button @click="deleteTask(n)">X</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { inject, onMounted, onUpdated, reactive, ref, watch, type Ref } from "vue";

interface Task_object {
  name: string;
  duration: number;
}
let tasks_array: Array<Task_object> = reactive([]);
let task_provided = reactive({ name: "", duration: 0 });
let task_index = ref(0);
let first = ref(true);
let mouse_on_task = ref({x: 0, y: 0}); 

function deleteTask(n: number) {
  tasks_array.splice(n, 1);
}

task_provided = inject("task_provide");


function moveTask(e: MouseEvent) {
  let task = document.getElementById("task"+task_index.value);

  if (task) {
    if (first.value == true) {
      mouse_on_task.value.x = e.pageX - task.getBoundingClientRect().left; 
      mouse_on_task.value.y = e.pageY - task.getBoundingClientRect().top; 
      first.value = false;
    }
    task.style.top = `${e.pageY - mouse_on_task.value.y}px`;
    task.style.left = `${e.pageX - mouse_on_task.value.x}px`;
  }
}

function taskDrag(n: number, mouse: boolean) {
  let app = document.getElementById("app");

  if (mouse) {
    console.log(n, mouse);
    app?.addEventListener("mousemove", moveTask)
  } else {
    console.log(n, mouse);
    app?.removeEventListener("mousemove", moveTask)
    first.value = true;
  }
}

watch(task_provided, () => {
  let task: Task_object = { name: "", duration: 0 };

  task.name = task_provided.name;
  task.duration = task_provided.duration;
  tasks_array.push(task);
});


onMounted(() => {});

onUpdated(() => {});
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
  width: 30%;
  height: auto;
  background-color: greenyellow;
  position: absolute;
}
</style>
