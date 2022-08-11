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
      {{ task.name }} {{ n }}
      {{ task.duration }}
      <button class="delete-task-btn" @click="deleteTask(n)">X</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  inject,
  onMounted,
  onUpdated,
  reactive,
  ref,
  watch,
  type Ref,
} from "vue";

interface Task_object {
  name: string;
  duration: number;
}
let tasks_array: Array<Task_object> = reactive([]);
let task_provided = reactive({ name: "", duration: 0 });
let task_index = ref(0);
let first = ref(true);
let mouse_on_task = ref({ x: 0, y: 0 });
let scroll_top = ref(0);
let previous_scroll = ref(-1);

function deleteTask(n: number) {
  tasks_array.splice(n, 1);
}

task_provided = inject("task_provide");

function moveTask(e: MouseEvent) {
  let task = document.getElementById("task" + task_index.value);
  let hours = document.getElementsByClassName('hour-wrapper');

  if (task) {
    if (first.value == true) {
      mouse_on_task.value.x = e.pageX - task.getBoundingClientRect().left;
      mouse_on_task.value.y = e.pageY - task.getBoundingClientRect().top;
      first.value = false;
    }
    task.style.top = `${e.pageY - mouse_on_task.value.y}px`;
    task.style.left = `${e.pageX - mouse_on_task.value.x}px`;
    if (isTasksOnCalendar(task_index.value)) {
      task.style.height = `${(hours[0].getBoundingClientRect().height / 60) * tasks_array[task_index.value].duration}px`;
    }
  }
}

function taskDrag(n: number, mouse: boolean) {
  let app = document.getElementById("app");

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

function isTasksOnCalendar(n: number) {
  let hours = document.getElementsByClassName("hour-wrapper");
  let calendar_wrapper = document.getElementById("calendar-wrapper");
  let task = document.getElementById("task" + n);

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
      task.style.height = `5%`;
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
  let heading =document.getElementById("heading") 
  if (heading) {
    heading_height = heading.getBoundingClientRect().height;
  }
  let not_on_calendar_height = 0;

  if (tasks && input) {
    let input_height = input.getBoundingClientRect().height;
    let input_left = input.getBoundingClientRect().left;

    for (let i = 0; i < tasks_array.length; i++) {
      if (!isTasksOnCalendar(i)) {
        if (not_on_calendar_height !== 0) {
          tasks[i].style.top = `${not_on_calendar_height + input_height}px`;
        } else {
          tasks[i].style.top = `${input_height + heading_height}px`;
          not_on_calendar_height = heading_height;
        }
        tasks[i].style.left = `${input_left}px`;
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

onMounted(() => {
  checkScroll();
});

onUpdated(() => {
  console.log("Tasks.vue updated");
  applyPositionToTask();
});
</script>

<style lang="scss" scoped>
#tasks {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #D9DAF2;
}
.task {
  margin-left: 2.5%;
  border-radius: 1.5vh;
  width: 25%;
  height: 20%;
  background-color: white;
  position: absolute;
  display: flex;
  justify-content: space-around;
  align-items: center;

  .delete-task-btn {
    height: 100%;
  }
}
</style>
