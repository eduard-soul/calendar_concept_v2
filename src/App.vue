<template>
  <header id="heading"><h1>plan your day</h1></header>
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
import { onMounted, onUpdated, provide, reactive } from "vue";

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
@import url("https://fonts.googleapis.com/css2?family=Josefin+Slab:ital@1&display=swap");

* {
  margin: 0;
  padding: 0;
}
header {
  width: 100%;
  height: 5%;
  background-color: #fff1e1;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: "Josefin Slab", serif;
  z-index: 100;
  position: relative;
}
#all-wrapper {
  display: flex;
  width: 100vw;
  height: calc(100vh - 5%);
  background-color: blue;
  user-select: none;

  #calendar-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 100%;
    width: 70%;
    background-color: #d0e9ee;
    overflow-y: scroll;
  }
  #tasks-wrapper {
    height: 100%;
    width: 30%;
    display: flex;
    flex-direction: column;
    background-color: #d9daf2;
  }
}
</style>
