<template>
  <div class="gantt-chart">
    <table>
      <thead>
        <tr>
          <th><div class="table-head-first-column"></div></th>
          <th v-for="time in columns">{{ time }}</th>
        </tr>
      </thead>
      <tbody v-for="d in data" :key="d.id">
        <tr v-if="d && d.task_data && d.task_data.length > 0">
          <td>{{ d.name }}</td>
          <Column v-for="time in columns" :time="time" :tasks="d.task_data" />
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script setup>
import { onMounted, ref } from "vue";
import dataJson from "/src/assets/data.json";
import Column from "./Column.vue";

let data = ref(dataJson.data);
const startTime = ref("08:00");
let columns = ref([]);

onMounted(() => {
  getColumns();
});

const getColumns = () => {
  const [startHours, startMinutes] = startTime.value.split(":");
  let currentHours = parseInt(startHours, 10);
  let currentMinutes = parseInt(startMinutes, 10);

  for (let i = 0; i < 49; i++) {
    const timeValue = formatTime(currentHours, currentMinutes);
    columns.value.push(timeValue);
    [currentHours, currentMinutes] = addTime(currentHours, currentMinutes);
  }
};

const formatTime = (hours, minutes) => {
  const formattedHours = String(hours).padStart(2, "0");
  const formattedMinutes = String(minutes).padStart(2, "0");
  return `${formattedHours}:${formattedMinutes}`;
};

const addTime = (hours, minutes) => {
  minutes += 15;
  if (minutes >= 60) {
    minutes -= 60;
    hours += 1;
  }
  return [hours, minutes];
};
</script>
<style>
.gantt-chart {
  width: 100%;
  overflow-x: auto;
}

table {
  width: 100%;
  border-radius: 16px;
  border-spacing: 0;
}

thead {
  background-color: #eaeaea;
  border-radius: 16px 16px 0 0;
}

tbody tr {
  height: 72px;
}

tbody td {
  border: 1px solid #eaeaea;
}

th {
  font-size: 16px;
  font-weight: 400;
  height: 48px;
}

thead th:first-child {
  background: #eaeaea;
}

th:not(:first-child) {
  border: 1px solid #ffffff;
}

tr > th:first-child,
tr > td:first-child {
  min-width: 140px;
  position: sticky;
  left: 0;
}

tr > th:first-child {
  background-color: #ffffff;
}

tr > td:first-child {
  background: #ffffff;
  border: 1px solid #eaeaea;
}

tr > th:not(:first-child),
tr > td:not(:first-child) {
  min-width: 80px;
}

td,
th {
  text-align: center;
}

tr > th:last-child {
  border-top-right-radius: 16px;
}

tbody:last-child tr:last-child td:first-child {
  border-bottom-left-radius: 16px;
}

tbody:last-child tr:last-child td:last-child {
  border-bottom-right-radius: 16px;
}

.table-head-first-column {
  width: 100%;
  height: inherit;
  border-left: 1px solid #eaeaea;
  border-top-left-radius: 16px;
  background-color: #eaeaea;
}
</style>
