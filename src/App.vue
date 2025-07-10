<script setup>
import { ref, onMounted, onUpdated } from "vue";
import axios from "axios";

const url = import.meta.env.VITE_API_URL;

console.log(url);

const apps = ref([]);
const tasks = ref([]);
const message = ref(null);
const showMessage = ref(true);

const appName = ref("");
const appType = ref("desktop");

const taskName = ref("");
const taskPriority = ref("medium");
const taskCategory = ref("frontend");
const taskStatus = ref("pending");

// apps

const getApps = async () => {
  try {
    const rawData = await axios.get(`${url}/api/apps`);
    if (rawData) {
      apps.value = rawData.data.data.apps;
      showMessage.value = true;
      message.value = "Get Apps Ok!";
    } else {
      showMessage.value = true;
      message.value = "Error fetching apps";
    }
  } catch (error) {
    message.value = error.message;
  }
};

const createApp = async () => {
  try {
    await axios.post(`${url}/api/apps`, {
      appName: appName.value,
      appType: appType.value,
    });
    showMessage.value = true;
    const msg = `App ${appName.value} created!`;
    await getApps();
    message.value = msg;
  } catch (error) {
    showMessage.value = true;
    message.value = error.message;
  }
  resetApp();
};

const removeApp = async (id, name) => {
  try {
    await axios.delete(`${url}/api/apps/${id}`, { data: { id } });
    const msg = `App ${name} removed!`;
    await getApps();
    showMessage.value = true;
    message.value = msg;
  } catch (error) {
    showMessage.value = true;
    message.value = error.message;
  }
};

const dropApps = async () => {
  try {
    await axios.delete(`${url}/api/apps/drop`);
    showMessage.value = true;
    const msg = `Apps list dropped!`;
    await getApps();
    message.value = msg;
  } catch (error) {
    showMessage.value = true;
    message.value = error.message;
  }
};

function resetApp() {
  appName.value = "";
  appType.value = "desktop";
}

// tasks

const getTasks = async () => {
  try {
    const rawData = await axios.get(`${url}/api/tasks`);
    if (rawData) {
      tasks.value = rawData.data.data.tasks;
      showMessage.value = true;
      message.value = `Tasks gets!`;
    }
  } catch (error) {
    showMessage.value = true;
    message.value = error.message;
  }
};

const createTask = async () => {
  try {
    await axios.post(`${url}/api/tasks/`, {
      task: {
        taskName: taskName.value,
        taskCategory: taskCategory.value,
        taskStatus: taskStatus.value,
        taskPriority: taskPriority.value,
      },
    });
    showMessage.value = true;
    const msg = `Task ${taskName.value} created!`;
    await getTasks();
    message.value = msg;
  } catch (error) {
    showMessage.value = true;
    message.value = error.message;
  }
  resetTask();
};

const removeTask = async (id, name) => {
  try {
    await axios.delete(`${url}/api/tasks/${id}`, { data: { id } });
    showMessage.value = true;
    const msg = `Task ${name} removed!`;
    await getTasks();
    message.value = msg;
  } catch (error) {
    showMessage.value = true;
    message.value = error.message;
  }
};

function resetTask() {
  taskName.value = "";
  taskCategory.value = "frontend";
  taskStatus.value = "pending";
  taskPriority.value = "medium";
}

const dropTasks = async () => {
  try {
    await axios.delete(`${url}/api/tasks/drop`);
    showMessage.value = true;
    const msg = `Tasks dropped!`;
    await getTasks();
    message.value = msg;
  } catch (error) {
    message.value = error.message;
  }
};

onMounted(async () => {
  try {
    await getApps();
    await getTasks();
    showMessage.value = false;
    message.value = null;
  } catch (error) {
    showMessage.value = true;
    message.value = error.message;
  }
});

onUpdated(() =>
  setTimeout(() => {
    showMessage.value = false;
  }, 3000),
);
</script>

<template>
  <div class="h-screen bg-gray-900 text-white">
    <div class="p-3 text-center text-2xl">TODO</div>
    <div class="h-12">
      <div
        class="p-4 text-center text-orange-400 transition-opacity duration-300"
        v-if="showMessage"
      >
        {{ message }}
      </div>
    </div>
    <div class="flex justify-center gap-2">
      <input type="text" v-model="appName" class="border" />
      <select v-model="appType" class="border">
        <option value="desktop">Desktop</option>
        <option value="mobile">Mobile</option>
        <option value="web">Web</option>
      </select>
      <button class="border hover:text-green-300" @click="createApp">
        Create
      </button>
      <button class="border hover:text-red-300" @click="resetApp">Reset</button>
    </div>
    <div class="flex gap-2">
      <div class="text-blue-400">Apps</div>
      <button class="cursor-pointer text-xs text-gray-400" @click="dropApps">
        Drop
      </button>
    </div>
    <ul>
      <li v-for="{ _id, appName, appType } in apps" class="flex gap-2">
        <div class="flex gap-4">
          <div>{{ _id }}</div>
          <div>{{ appName }}</div>
          <div>{{ appType }}</div>
        </div>
        <button
          class="text-red-300 hover:text-red-400"
          @click="removeApp(_id, appName)"
        >
          Remove
        </button>
      </li>
    </ul>
    <hr />
    <div class="m-2 flex justify-center gap-2">
      <input type="text" v-model="taskName" class="border" />
      <select v-model="taskCategory" class="border">
        <option value="frontend">Frontend</option>
        <option value="backend">Backend</option>
        <option value="testing">Testing</option>
      </select>
      <select v-model="taskStatus" class="border">
        <option value="pending">pending</option>
        <option value="inProgress">inProgress</option>
        <option value="done">Done</option>
      </select>
      <select v-model="taskPriority" class="border">
        <option value="high">high</option>
        <option value="medium">medium</option>
        <option value="low">low</option>
      </select>
      <button class="border hover:text-green-300" @click="createTask">
        Create
      </button>
      <button class="border hover:text-red-300" @click="resetTask">
        Reset
      </button>
    </div>
    <div class="flex gap-2">
      <div class="text-blue-400">Tasks</div>
      <button class="cursor-pointer text-xs text-gray-400" @click="dropTasks">
        Drop
      </button>
    </div>
    <ul>
      <li
        v-for="{
          _id,
          taskName,
          taskCategory,
          taskPriority,
          taskStatus,
        } in tasks"
        class="flex gap-2"
      >
        <div class="flex gap-4">
          <div>{{ _id }}</div>
          <div>{{ taskName }}</div>
          <div>{{ taskCategory }}</div>
          <div>{{ taskPriority }}</div>
          <div>{{ taskStatus }}</div>
        </div>
        <button
          class="text-red-300 hover:text-red-400"
          @click="removeTask(_id, taskName)"
        >
          Remove
        </button>
      </li>
    </ul>
  </div>
</template>

<style lang="scss" scoped></style>
