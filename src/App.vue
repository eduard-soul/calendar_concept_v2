<template>
  <div id="all-wrapper">
    <section id="calendar-wrapper">
      <Calendar></Calendar>
    </section>
    <section id="tasks-wrapper">
      <AddTask></AddTask>
      <Tasks></Tasks>
    </section>
  </div>
</template>

<script setup lang="ts">
  import Calendar from "./Calendar.vue"
  import AddTask from "./AddTask.vue"
  import Tasks from "./Tasks.vue"
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
</style>
