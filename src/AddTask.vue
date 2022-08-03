<template>
  <div id="input-wrapper">
    <input @keydown.enter="addTask()" id="input-task" type="text" v-model="input" autofocus>
  </div>
</template>

<script setup lang="ts">
  import { onUpdated, ref} from 'vue';

  let input = ref("");

  function parseInput(input: string) {
    let task = {name: "", duration: 0};

    if (input !== "") {
      let i = 0;
      for (i; i < input.length && input[i] !== ':'; i++) {
        task.name += input[i];
      }
      i++;
      for (i; i < input.length && input[i] >= '0' && input[i] <= '9'; i++) {
          task.duration *= 10;
          task.duration += parseInt(input[i]);
      }
      if (input[i] == 'h') {
        task.duration *= 60;
      }
      if (task.name && task.duration) {
        return (task);
      }
    }
    return (null!);
  }

  function addTask() {
    let task = {name: "", duration: 0};
    
    task = parseInput(input.value);
    if (task) {
      console.log("Right task");
    }
    else {
      console.log("Invalid task");
    }
  }

  onUpdated(() => {
  })
</script>

<style lang="scss" scoped>
  #input-wrapper {
    width: 100%;
    height: 10%;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: grey;

    #input-task {
      width: 80%;
    }
  }
</style>
