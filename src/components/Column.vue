<template>
  <td :colspan="span" v-if="!hide">
    <div
      :class="isFilled ? 'filled' : ''"
      :style="{ color: color, backgroundColor: backgroundColor }"
    >
      {{ taskName }}
    </div>
  </td>
</template>
<script setup>
import { defineProps, onMounted, ref } from "vue";
import { COLORS, TASK_STATUS } from "../utils/constants.js";
const props = defineProps(["time", "tasks"]);

const time = ref(props.time);
const tasks = ref(props.tasks);

const taskStatus = ref(TASK_STATUS);
const colors = ref(COLORS);

let taskId = ref();
let taskName = ref("");
let span = ref(1);
let backgroundColor = ref("");
let color = ref("");
let isFilled = ref(false);
let hide = ref(false);

onMounted(() => {
  determineFilled();
});

const determineFilled = () => {
  if (!tasks.value) return;

  const matchingTask = tasks.value.find(
    (task) => time.value === task.start_time,
  );
  if (matchingTask) {
    fillColor(matchingTask);
    isFilled.value = true;
    taskId.value = matchingTask.id;
    taskName.value = matchingTask.name;
    span.value = getSpanNumber(matchingTask.start_time, matchingTask.end_time);
  } else {
    const taskToHide = tasks.value.find(
      (task) => time.value > task.start_time && time.value < task.end_time,
    );
    if (taskToHide) {
      hide.value = true;
    }
  }
};

const fillColor = (task) => {
  switch (task.status) {
    case taskStatus.value["COMPLETED"]:
      backgroundColor.value = colors.value.COMPLETED.BACKGROUND;
      color.value = colors.value.COMPLETED.TEXT;
      break;
    case taskStatus.value["IN_PROGRESS"]:
      backgroundColor.value = colors.value.IN_PROGRESS.BACKGROUND;
      color.value = colors.value.IN_PROGRESS.TEXT;
      break;
    case taskStatus.value["NOT_STARTED_YET"]:
      backgroundColor.value = colors.value.NOT_STARTED.BACKGROUND;
      color.value = colors.value.NOT_STARTED.TEXT;
      break;
    case taskStatus.value["FUTURE"]:
      backgroundColor.value = colors.value.FUTURE.BACKGROUND;
      color.value = colors.value.FUTURE.TEXT;
      break;
  }
};

const getSpanNumber = (taskStartTime, taskEndTime) => {
  let startDate = convertTimeToDate(taskStartTime);
  let endDate = convertTimeToDate(taskEndTime);

  return (endDate - startDate) / (1000 * 60) / 15;
};

const convertTimeToDate = (t) => {
  const timeParts = t.split(":");
  return new Date(0, 0, 0, timeParts[0], timeParts[1]);
};
</script>

<style>
.filled {
  display: flex;
  height: 65px;
  justify-content: center;
  align-items: center;
  border-radius: 8px;
  width: 100%;
  cursor: pointer;
}
</style>
