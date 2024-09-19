<template>
  <v-container fluid class="app-background">
    <v-row justify="center">
      <v-col cols="12" sm="8" md="6">
        <v-card class="pa-5">
          <v-card-title class="text-center">
            <h1>To-Do Tasks</h1>
          </v-card-title>
          <v-text-field
            label="Add a task"
            v-model="newTask"
            @keyup.enter="addTask"
            clearable
            class="mb-4"
          ></v-text-field>
          <v-btn color="primary" @click="addTask" block>Add Task</v-btn>

          <v-divider class="my-4"></v-divider>

          <div class="task-list">
            <v-list dense>
              <template v-if="tasks.length === 0">
                <v-list-item>
                  <v-list-item-content class="text-center">
                    <v-list-item-title>No tasks available</v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </template>

              <template v-else>
                <v-list-item
                  v-for="(task, index) in tasks"
                  :key="index"
                  :class="{'text-decoration-line-through': task.completed}"
                  class="d-flex align-center"
                >
                  <v-list-item-content @click="toggleTask(index)" class="flex-grow-1">
                    <v-list-item-title v-if="!task.editing">
                      {{ task.name }}
                    </v-list-item-title>
                    <v-text-field
                      v-else
                      v-model="task.editText"
                      @keyup.enter="saveEdit(index)"
                      @blur="saveEdit(index)"
                      :autofocus="true"
                    ></v-text-field>
                  </v-list-item-content>
                  <v-list-item-action>
                    <v-btn @click="deleteTask(index)" class="ma-1 mt-4" color="red">
                      Delete
                    </v-btn>
                    <v-btn @click="editTask(index)" class="ma-1 mt-4">
                      Edit
                    </v-btn>
                  </v-list-item-action>
                </v-list-item>
              </template>
            </v-list>
          </div>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>



<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: []
    };
  },
  created() {
    this.loadTasks(); // Load tasks from local storage when the component is created
  },
  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        this.tasks.push({ name: this.newTask, completed: false, editing: false, editText: this.newTask });
        this.newTask = '';
        this.saveTasks(); // Save tasks to local storage after adding
      }
    },
    toggleTask(index) {
      this.tasks[index].completed = !this.tasks[index].completed;
      this.saveTasks(); // Save tasks to local storage after toggling
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks(); // Save tasks to local storage after deleting
    },
    editTask(index) {
      this.tasks[index].editing = true;
      this.tasks[index].editText = this.tasks[index].name;
    },
    saveEdit(index) {
      if (this.tasks[index].editText.trim() !== '') {
        this.tasks[index].name = this.tasks[index].editText;
        this.tasks[index].editing = false;
        this.saveTasks(); // Save tasks to local storage after editing
      }
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks)); // Save tasks to local storage
    },
    loadTasks() {
      const savedTasks = localStorage.getItem('tasks'); // Load tasks from local storage
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks); // Parse and set tasks
      }
    }
  }
};
</script>



<style scoped>
.app-background {
  background-image: url('/bgg.png'); /* changed from background-color */
  background-size: cover; /* Ensures the background image covers the entire container */
  background-position: center;
  background-repeat: no-repeat;
  min-height: 100vh; /* Ensures the container is at least as tall as the viewport */
}

.text-decoration-line-through {
  text-decoration: line-through;
}

.v-card {
  /* background-color: transparent !important; Make the background transparent */
  box-shadow: none !important; /* Remove any box shadow if present */
  border: none !important; /* Remove any border if present */
  margin-top:80px ;
  border-radius: 20px;
}

/* Custom styling for the task list */
.task-list {
  max-height: 400px; /* Adjust the maximum height as needed */
  overflow-y: auto; /* Enables vertical scrolling when content exceeds max-height */
}
</style>

