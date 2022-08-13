<template>
  <div id="tasks">
    <div
      v-for="(task, n) in tasks_array"
      v-bind:key="n"
      :id="'task' + n"
      class="task"
      @mousedown="taskDrag(n, true), (task_index = n)"
      @mouseup="taskDrag(n, false), (task_index = n)"
    >
      <div class="task-props-wrapper">
        <p class="task-name">{{ task.name }}</p>
        <p class="task-duration">
          {{
            task.duration > 60 ? task.duration / 60 + "h" : task.duration + "m"
          }}
        </p>
      </div>
      <button
        class="delete-task-btn"
        :id="'delete-btn' + n"
        @click="deleteTask(n)"
        @mouseenter="mouse_over_delete = true"
        @mouseleave="mouse_over_delete = false"
      >
        X
      </button>
    </div>
    <div id="explanation">
      <p>To add a task : <br><br> 
          - Type the task name <br>
          - Add the caracther&ensp;‘:’ <br>
          - And type the duration <span id="additional-infos">(by default in minutes, 
          add 'h' after the duration to make the duration in hours)</span></p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { inject, onMounted, onUpdated, reactive, ref, watch } from "vue";

interface Task_object {
  name: string;
  duration: number;
}
let tasks_array: Array<Task_object> = reactive([]);
let task_provided = reactive({ name: "", duration: 0 });
let task_index = ref(0);
let first = ref(true);
let mouse_on_task = ref({ x: 0, y: 0 });
let previous_scroll = ref(-1);
let from_calendar_to_tasks = ref(false);
let mouse_over_delete = ref(false);

function deleteTask(n: number) {
  console.log('deleteTask');
  for (let i = n; i < tasks_array.length - 1; i++) {
    let task1 = document.getElementById('task' + i);
    let task2 = document.getElementById('task' + (i + 1));

    console.log("task1 = task2");
    if (task1 && task2) {
      task1.style.left = task2.style.left;
      task1.style.top = task2.style.top;
      task1.style.height = task2.style.height;
    }
  }
  tasks_array.splice(n, 1);
  applyPositionToTask();
}

task_provided = inject("task_provide");

function moveTask(e: MouseEvent) {
  let task = document.getElementById("task" + task_index.value);
  let hours = document.getElementsByClassName("hour-wrapper");
  let top_from_calendar = 0;

  console.log("moveTask");
  if (task) {
    if (first.value == true) {
      mouse_on_task.value.x = e.pageX - task.getBoundingClientRect().left;
      mouse_on_task.value.y = e.pageY - task.getBoundingClientRect().top;
      first.value = false;
      // Maybe check here if the task is taken from calendar if so when not on calendar calc top
      if (isTasksOnCalendar(task_index.value)) {
        from_calendar_to_tasks.value = true;
      } else {
        from_calendar_to_tasks.value = false;
      }
    }
    task.style.left = `${e.pageX - mouse_on_task.value.x}px`;
    task.style.top = `${e.pageY - mouse_on_task.value.y}px`;
    if (isTasksOnCalendar(task_index.value)) {
      task.style.height = `${
        (hours[0].getBoundingClientRect().height / 60) *
        tasks_array[task_index.value].duration
      }px`;
    } else if (from_calendar_to_tasks.value == true) {
      task.style.top = `${
        e.pageY -
        (mouse_on_task.value.y /
          ((hours[0].getBoundingClientRect().height / 60) *
            tasks_array[task_index.value].duration)) *
          task.getBoundingClientRect().height
      }px`;
    }
  }
}

function taskDrag(n: number, mouse: boolean) {
  let app = document.getElementById("app");
  let delete_btn = document.getElementById("delete-btn" + n);

  console.log(mouse_over_delete.value);
  if (!mouse_over_delete.value) {
    console.log("taskDrag");
    if (!app) {
      console.error("document.getElementById('app')");
    } else if (mouse) {
      app.addEventListener("mousemove", moveTask);
    } else {
      app.removeEventListener("mousemove", moveTask);
      applyPositionToTask();
      first.value = true;
    }
  }
}

function isTasksOnCalendar(n: number) {
  let hours = document.getElementsByClassName("hour-wrapper");
  let calendar_wrapper = document.getElementById("calendar-wrapper");
  let task = document.getElementById("task" + n);

  console.log("isTasksOnCalendar");
  if (hours && calendar_wrapper && task) {
    if (
      task.getBoundingClientRect().top >=
        hours[0].getBoundingClientRect().top &&
      task.getBoundingClientRect().top <=
        hours[23].getBoundingClientRect().bottom &&
      task.getBoundingClientRect().left >=
        hours[0].getBoundingClientRect().left &&
      task.getBoundingClientRect().left <=
        hours[0].getBoundingClientRect().right
    ) {
      return 1;
    } else {
      task.style.height = `7%`;
      return 0;
    }
  }
}

function whereIsTask(n: number) {
  let where = { hour: 0, quarter: 0 };
  let task = document.getElementById("task" + n);
  let hours = Array.from(
    document.getElementsByClassName(
      "hour-wrapper"
    ) as HTMLCollectionOf<HTMLElement>
  );

  console.log("whereIsTask");
  if (task) {
    let i = 0;
    if (
      task.getBoundingClientRect().top >= hours[12].getBoundingClientRect().top
    ) {
      i = 12;
    }
    for (i; i < hours.length; i++) {
      if (
        task.getBoundingClientRect().top <= hours[i].getBoundingClientRect().top
      ) {
        break;
      }
    }
    i--;
    where.hour = i;
    where.quarter = Math.floor(
      (task.getBoundingClientRect().top -
        hours[i].getBoundingClientRect().top) /
        (hours[i].getBoundingClientRect().height / 4)
    );
  }

  return where;
}

function putTaskOnQuarter(n: number, where: { hour: number; quarter: number }) {
  let quarter = document.getElementById(
    "h" + where.hour + "quarter" + where.quarter
  );
  let task = document.getElementById("task" + n);
  console.log("putTaskOnQuarter");

  if (quarter && task) {
    task.style.top = `${quarter.getBoundingClientRect().top}px`;
    task.style.left = `${quarter.getBoundingClientRect().left}px`;
  }
}

function applyPositionToTask() {
  let tasks = Array.from(
    document.getElementsByClassName("task") as HTMLCollectionOf<HTMLElement>
  );
  let input = document.getElementById("input-wrapper");
  let heading_height = 0;
  let heading = document.getElementById("heading");
  if (heading) {
    heading_height = heading.getBoundingClientRect().height;
  }
  let not_on_calendar_height = 0;

  console.log("applyPositionToTask");
  if (tasks && input) {
    let input_height = input.getBoundingClientRect().height;
    let input_left = input.getBoundingClientRect().left;

    for (let i = 0; i < tasks_array.length; i++) {
      // console.log(tasks[i]);
      if (!isTasksOnCalendar(i)) {
        if (not_on_calendar_height !== 0) {
          tasks[i].style.top = `${not_on_calendar_height + input_height}px`;
        } else {
          tasks[i].style.top = `${input_height + heading_height}px`;
          not_on_calendar_height = heading_height;
        }
        tasks[i].style.left = ``;
        not_on_calendar_height += tasks[i].getBoundingClientRect().height * 1.1;
      } else {
        putTaskOnQuarter(i, whereIsTask(i));
      }
    }
  }
}

function checkScroll() {
  let calendar = document.getElementById("calendar-wrapper");
  let hours = document.getElementsByClassName("hour-wrapper");
  let task;

  console.log("checkScroll");
  if (calendar) {
    calendar.addEventListener("scroll", () => {
      if (calendar) {
        if (previous_scroll.value == -1) {
          previous_scroll.value = calendar.scrollTop;
        }
        for (let i = 0; i < tasks_array.length; i++) {
          task = document.getElementById("task" + i);

          if (task && calendar) {
            if (
              task.getBoundingClientRect().top -
                (calendar.scrollTop - previous_scroll.value) >=
                hours[0].getBoundingClientRect().top &&
              task.getBoundingClientRect().top -
                (calendar.scrollTop - previous_scroll.value) <=
                hours[23].getBoundingClientRect().bottom &&
              task.getBoundingClientRect().left >=
                hours[0].getBoundingClientRect().left &&
              task.getBoundingClientRect().left <=
                hours[0].getBoundingClientRect().right
            ) {
              task.style.top = `${
                parseInt(task.style.top) -
                (calendar.scrollTop - previous_scroll.value)
              }px`;
            }
          }
        }
        previous_scroll.value = calendar.scrollTop;
      }
    });
  }
}

watch(task_provided, () => {
  let task: Task_object = { name: "", duration: 0 };

  task.name = task_provided.name;
  task.duration = task_provided.duration;
  tasks_array.push(task);
});

window.addEventListener('resize', () => {
  applyPositionToTask()
});

onMounted(() => {
  checkScroll();
});

onUpdated(() => {
  console.log("Tasks.vue updated");
  if (!mouse_over_delete.value) {
    applyPositionToTask();
  }
  mouse_over_delete.value = false;
});
</script>

<style lang="scss" scoped>
@import url("https://fonts.googleapis.com/css2?family=Kantumruy+Pro:wght@400;500&display=swap");
@import url('https://fonts.googleapis.com/css2?family=Josefin+Slab:wght@300&display=swap');

#tasks {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
  background-color: #d9daf2;

  #explanation {
    width: 80%;
    height: 30%;
    padding: 10%;
    padding-bottom: 20%;
    color: black;
    font-size: calc(1vh + 1vw);
    font-family: 'Josefin Slab', serif;

    #additional-infos {
      color: grey;
    }
  }
}
.task {
  border-radius: 1.5vh;
  width: 24%;
  height: 20%;
  background-color: white;
  position: absolute;
  display: flex;
  justify-content: space-around;
  align-items: center;

  .task-props-wrapper {
    display: flex;
    width: 75%;
    justify-content: space-between;
  }

  .task-name,
  .task-duration {
    font-family: "Kantumruy Pro", sans-serif;
    font-size: 200%;
    font-weight: 500;
  }
  .task-name {
    margin-left: 3%;
  }
  .task-duration {
    color: grey;
  }
  .delete-task-btn:hover {
    cursor: pointer;
    color: red;
  }
  .delete-task-btn {
    height: 100%;
    width: 10%;
    font-size: 150%;
    border: none;
    color: #f69ca0;
    font-weight: 600;
    background-color: transparent;
  }
}
</style>
