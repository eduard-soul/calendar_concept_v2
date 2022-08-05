<template>
  <div
    class="hour-wrapper"
    v-for="i in 24"
    v-bind:key="i"
    :id="'hour' + (i - 1)"
  >
    <h1>{{ i - 1 }}</h1>
  </div>
</template>

<script setup lang="ts"></script>

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
}
#hour0 {
  margin-top: 1%;
  border-top: 0.5vh solid black;
}
#hour23 {
  margin-bottom: 1%;
  border-bottom: 0.5vh solid black;
}
</style>
