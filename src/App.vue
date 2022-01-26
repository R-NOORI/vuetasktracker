<template>
  <div class="container">
    <Header
      :showAddTask="addTaskVisibility"
      @toggle-add-task="toggleAddTask"
      title="Task Tracker"
    />
    <div v-show="addTaskVisibility">
      <AddTask @add-task="addTask" />
    </div>
    <Tasks
      @toggle-reminder="toggleReminder"
      @delete-task="handleDelete"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";
import Swal from "sweetalert2";

const STORAGE_KEY = "local_task";

export default {
  name: "App",
  components: {
    Header,
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
      addTaskVisibility: false,
    };
  },
  methods: {
    toggleAddTask() {
      this.addTaskVisibility = !this.addTaskVisibility;
    },
    handleDelete(id) {
      Swal.fire({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, delete it!",
      }).then((result) => {
        if (result.isConfirmed) {
          this.tasks = this.tasks.filter((task) => task.id != id);
          localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
          Swal.fire("Deleted!", "Your file has been deleted.", "success");
        }
      });
    },
    toggleReminder(id) {
      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: !task.reminder } : task
      );
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
    },
    addTask(task) {
      this.tasks = [...this.tasks, task];
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
    },
  },
  created() {
    this.tasks = JSON.parse(localStorage.getItem(STORAGE_KEY));
  },
};
</script>

<style>
* {
  padding: 0%;
  margin: 0%;
  box-sizing: border-box;
}

body {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: dodgerblue;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
  border-radius: 5px;
}
.btn:focus {
  outline: none;
}

.btn:active {
  transform: scale(0.98);
}

.btn-block {
  display: block;
  width: 100%;
}
</style>
