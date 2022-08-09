<template>
  <div id="all-wrapper">
    <section id="calendar-wrapper">
      <Calendar></Calendar>
    </section>
    <section id="tasks-wrapper">
      <AddTask
        @taskEmited="
          (task) => {
            provideTask(task);
          }
        "
      ></AddTask>
      <Tasks></Tasks>
    </section>
  </div>
</template>

<script setup lang="ts">
import Calendar from "./Calendar.vue";
import AddTask from "./AddTask.vue";
import Tasks from "./Tasks.vue";
import { onMounted, onUpdated, provide, reactive, ref } from "vue";

let task_provide = reactive({ name: "", duration: 0 });
provide("task_provide", task_provide);

function provideTask(task) {
  task_provide.name = task.name;
  task_provide.duration = task.duration;
}

function initialScrollPosition() {
  let calendar_wrapper = document.getElementById("calendar-wrapper");

  if (calendar_wrapper) {
    calendar_wrapper.scrollTop = calendar_wrapper.scrollHeight / 4;
  }
}

onMounted(() => {
  initialScrollPosition();
});
onUpdated(() => {});
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
}
#all-wrapper {
  display: flex;
  width: 100vw;
  height: 100vh;
  background-color: blue;
  user-select: none;

  #calendar-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 100%;
    width: 70%;
    background-color: red;
    overflow-y: scroll;
  }
  #tasks-wrapper {
    height: 100%;
    width: 30%;
    display: flex;
    flex-direction: column;
    background-color: green;
  }
}
button {
  height: 100px;
}
</style>
