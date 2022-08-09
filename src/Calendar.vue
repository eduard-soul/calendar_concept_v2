<template>
  <div
    class="hour-wrapper"
    v-for="i in 24"
    v-bind:key="i"
    :id="'hour' + (i - 1)"
  >
    <h1>{{ i - 1 }}</h1>
    <div class="quarter-wrapper">
      <div class="quarter" :id="'h' + (i - 1) + 'quarter' + 0"></div>
      <div class="quarter" :id="'h' + (i - 1) + 'quarter' + 1"></div>
      <div class="quarter" :id="'h' + (i - 1) + 'quarter' + 2"></div>
      <div class="quarter" :id="'h' + (i - 1) + 'quarter' + 3"></div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onUpdated } from "vue";

onUpdated(() => {
  console.log("Calendar update");
});
</script>

<style lang="scss" scoped>
.hour-wrapper {
  height: 7%;
  width: 90%;
  background-color: whitesmoke;
  border-radius: 2vh;
  padding: 1vh;
  border-left: 0.5vh solid black;
  border-right: 0.5vh solid black;
  border-top: 0.25vh solid black;
  border-bottom: 0.25vh solid black;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
#hour0 {
  margin-top: 1%;
  border-top: 0.5vh solid black;
}
#hour23 {
  margin-bottom: 1%;
  border-bottom: 0.5vh solid black;
}
.quarter-wrapper {
  height: calc(100% + 2vh);
  width: 90%;
  .quarter {
    width: 100%;
    height: 25%;
    border-bottom: 0.1vh grey solid;
  }
  #h23quarter3 {
    border-bottom: 0 grey solid;
  }
}
</style>
