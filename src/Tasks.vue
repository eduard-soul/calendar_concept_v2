<template>
  <div id="tasks">
    <div
      v-for="(task, n) in tasks_array"
      v-bind:key="n"
      :id="'task' + n"
      class="task"
      @mousedown="taskDrag(n, true)"
      @mouseup="taskDrag(n, false)"
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
let mouse_move_listener:EventListener;

function deleteTask(n: number) {
  tasks_array.splice(n, 1);
}

task_provided = inject("task_provide");

watch(task_provided, () => {
  let task: Task_object = { name: "", duration: 0 };

  task.name = task_provided.name;
  task.duration = task_provided.duration;
  tasks_array.push(task);
});

function taskDrag(n: number, drag: boolean) {
  let first = ref(true);
  let mouse_on_task = ref({x: 0, y: 0});
  let app = document.getElementById("app");

function watchMouseMouve(e:MouseEvent, n: number, first: Ref, mouse_on_task: Ref) { 
  let task = document.getElementById('task'+n);

  console.log("watchMouse");
  if (task) {
    if  (first.value == true) {
      mouse_on_task.value = {x: e.pageX - task.getBoundingClientRect().left , y: e.pageY - task.getBoundingClientRect().top};

      first.value = false;
      console.log(mouse_on_task.value.x, mouse_on_task.value.y);
    }
    task.style.top = `${e.pageY - mouse_on_task.value.y}px`;
    task.style.left = `${e.pageX - mouse_on_task.value.x}px`;
    // console.log(mouse_on_task.value.x, mouse_on_task.value.y);
  }
}

  function test(e) {
    watchMouseMouve(e, n, first, mouse_on_task);
  }

  if (drag) {
    console.log(drag);
    app.addEventListener('mousemove', test, {passive: true});
  } else if (!drag) {
    console.log(drag);
    app.removeEventListener('mousemove', test, {passive: true});
  }
}

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
