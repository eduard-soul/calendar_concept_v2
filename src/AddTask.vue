<template>
  <div id="input-wrapper">
    <input
      @keydown.enter="addTask()"
      id="input-task"
      type="text"
      v-model="input"
      autofocus
    />
    <div id="input-separator"></div>
  </div>
</template>

<script setup lang="ts">
import { onUpdated, provide, ref } from "vue";

const emit = defineEmits(["taskEmited"]);
let input = ref("");

function parseInput(input: string) {
  let task = { name: "", duration: 0 };

  if (input !== "") {
    let i = 0;
    for (i; i < input.length && input[i] !== ":"; i++) {
      task.name += input[i];
    }
    i++;
    for (i; i < input.length && input[i] >= "0" && input[i] <= "9"; i++) {
      task.duration *= 10;
      task.duration += parseInt(input[i]);
    }
    if (input[i] == "h") {
      task.duration *= 60;
    }
    if (task.name && task.duration) {
      return task;
    }
  }
  return null!;
}

function addTask() {
  let task = { name: "", duration: 0 };

  task = parseInput(input.value);
  if (task) {
    console.log("Right task");
    input.value = "";
    emit("taskEmited", task);
  } else {
    console.log("Invalid task");
  }
}

onUpdated(() => {});
</script>

<style lang="scss" scoped>
#input-wrapper {
  padding-top: 2.5%;
  width: 100%;
  height: 15%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

  #input-task {
    outline: none;
    height: 50%;
    width: 70%;
    border: none;
    border-radius: 1vh;
    font-size: 100%;
    padding-left: 10%;
    background-color: white;
  }
  #input-separator {
    width: 30%;
    height: 2%;
    margin-top: 5%;
    background-color: grey;
    opacity: 45%;
  }
}
</style>
