<template>
  <div class="flex container" style="height: 100%">
    <!-- left -->
    <div id="list" class="border border-black mr-2 w-1/4" style="height: 500px">
      <button @click="showInput" class="bg-green-600 p-1 text-white mb-6">Add Task</button>
      <div>
        <ul>
          <li v-for="(task, index) in tasks" :key="index" class="flex justify-between mb-2 items-center">
            <button class="border border-black p-1 w-48" @click="showSingleInput(index)">{{ task.title }}</button>
            <button class="red-button" @click="deleteTask(index)">Delete</button>
          </li>
        </ul>
      </div>
    </div>

    <!-- right -->
    <div id="single-list" class="border border-red-600 w-3/4">
      <!-- show tasks for single Task -->
      <div v-show="singleClicked" class="m-4">
        <div class="flex">
          <input type="text" name="task" v-model="newSingleInput">
          <div>
            <button @click="addSingleTask()" class="orange_button">Add task</button>
            <button @click="cancelSingle" class="blue-button">cancel</button>
          </div>
        </div>
        <div>

          <ul>
            <div v-if="this.show !== null || singleClicked == true" class="mt-4">
              <li v-for="(list, index) in tasks[this.show].list" :key="index" class="flex border border-gray-600 mb-2 items-center">
                <div class="flex items-center bg-blue-600 p-2 mr-4">
                  <button :value="list" class="mr-2" :id="index" @click="sortList(index, list)">()</button>
                  <h3>Priority</h3>
                </div>
                <div class="flex justify-between w-full">
                  <h1 v-bind:style="[checked.includes(list) ? {backgroundColor: 'red'} : {}]" class="px-2">{{ list }}</h1>
                  <button class="red-button mr-2" @click="cancelSingleTask(index, list)">Done</button>
                </div>
              </li>
            </div>
          </ul>

        </div>
      </div>

      <!-- create new task -->
      <div class="m-4" v-show="isClicked">
        <label class="text-xl">Task Name</label>
        <div>
          <input type="text" v-model="newTask">
          <button class="orange_button" @click="addTask()">Save</button>
          <button class="blue-button" @click="cancel">Cancel</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',

  data() {
    return {
      isClicked: false,
      singleClicked: false,
      show: null,

      newTask: '',
      newSingleInput: '',
      checked: [],

      tasks: [{
        title: '',
        list: []
      }]
    }
  },

  watch: {
  },

  mounted () {
    if (localStorage.getItem('tasks')) {
      try {
        this.tasks = JSON.parse(localStorage.getItem('tasks'))
      } catch (e) {
        localStorage.removeItem('tasks')
      }
    }

    if (localStorage.getItem('checked')) {
      try {
        this.checked = JSON.parse(localStorage.getItem('checked'))
      } catch (e) {
        localStorage.removeItem('checked')
      }
    }
  },

  methods: {
    sortList(index, list) {
      if (this.checked.includes(list) !== true) {
        this.tasks[this.show].list.unshift(this.tasks[this.show].list[index])
        this.tasks[this.show].list.splice(index+1, 1)
        this.checked.push(list)
      } else {
        var pointer = this.checked.indexOf(list)
        this.checked.splice(pointer, 1)
        this.tasks[this.show].list.push(list)
        this.tasks[this.show].list.splice(index, 1)
      }
      this.saveTasks()
      this.saveSingleTasks()
    },

    showInput() {
      if (this.singleClicked === true) {
        this.singleClicked = false
        this.isClicked = true;
      } else {
        this.isClicked = true
      }
    },

    showSingleInput(index) {
      this.show = index;
      if (this.isClicked === true) {
        this.isClicked = false;
        this.singleClicked = true
      } else {
        this.singleClicked = true
      }
    },

    addTask() {
      if (!this.newTask) {
        return;
      }

      this.tasks.push({title: this.newTask, list: []})
      this.newTask = ''
      this.saveTasks()
    },

    addSingleTask() {
      if (!this.newSingleInput) {
        return;
      }

      this.tasks[this.show].list.push(this.newSingleInput)
      this.newSingleInput = ''
      this.show = null
      this.saveTasks()
    },

    deleteTask(index) {
      this.tasks.splice(index, 1)
      this.saveTasks()
    },

    saveTasks() {
      const parsed = JSON.stringify(this.tasks)
      localStorage.setItem('tasks', parsed)
      this.isClicked = false
    },

    saveSingleTasks() {
      const parsedChecked = JSON.stringify(this.checked)
      localStorage.setItem('checked', parsedChecked)
    },

    cancel() {
      this.isClicked = false
      this.newTask = ''
    },

    cancelSingle() {
      this.singleClicked = false
      this.newSingleInput = ''
    },

    cancelSingleTask(index, list) {
      this.tasks[this.show].list.splice(index, 1)
      var value = this.checked.indexOf(list)
      this.checked.splice(value, 1)
      this.saveSingleTasks()
      this.saveTasks()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#list {
  padding: 10px;
}
/*border border-gray-500 p-1 focus:outline-none font-light*/
input {
  border: solid gray 1px;
  padding: 5px;
  font-weight: 300;
}
input:focus {
  border: solid gray 1px;
  padding: 5px;
  font-weight: 300;
  outline: none;
}
/*p-1 ml-4 bg-yellow-600 w-16 text-white*/
.orange_button {
  padding: 0.25rem;
  margin-left: 1rem;
  --tw-bg-opacity: 1;
  background-color: rgba(217, 119, 6, var(--tw-bg-opacity));
  width: 6rem;
  color: white;
}
/*p-1 ml-4 bg-blue-600 w-16 text-white*/
.blue-button {
  padding: 0.25rem;
  margin-left: 1rem;
  --tw-bg-opacity: 1;
  background-color: rgba(37, 99, 235, var(--tw-bg-opacity));
  width: 4rem;
  color: white;
}
/*bg-red-600 text-white p-1*/
.red-button {
  --tw-bg-opacity: 1;
  background-color: rgba(220, 38, 38, var(--tw-bg-opacity));
  color: white;  padding: 0.25rem;
  padding: 0.25rem;
}
</style>
