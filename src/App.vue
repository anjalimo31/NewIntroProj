<template>
  <body>
    
    <h1> {{ title }} </h1>
    <img :src="logoURL" :alt="logoCaption" width="400" height="400" />
    <br>
    <h2>Add a new task</h2>

    <div>
      <span>You have {{ allTasks }} {{ allTasks > 1? 'tasks' : 'task' }} at the moment</span>
      <br>
      <input type="text"
        v-model = "newTask"
        @keyup.enter = "addTask"
        placeholder = "Add a new task"
        >

      <button
        @click="addTask"
        :disabled="newTask.length < 1"
      >
        Add task
      </button>
    </div>

    <div v-if="newTask.length > 0">
      <h3>New task preview</h3>
      <p>{{ newTask }}</p>
    </div>

    <ul>
      <li 
        v-for="(task, index) in latest" 
        :key="task.id"
        @click="finishTask(task)"
        :class="{ strikeout: task.finished }"
      >
        {{ index + 1 }}. {{ task.name }}

        <div v-if="task.finished">
          <button @click="removeTask(task.id)">Delete task</button>
        </div>
      </li>
    </ul>
</body>
</template>

<script>

export default {
  data() {
    return {
      title: 'My To Do App',
      newTask: '',
      logoURL: 'https://images.unsplash.com/photo-1507925921958-8a62f3d1a50d?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=1955&q=80',
      logoCaption: 'A photo by Kelly Sikkema on Unsplash showing post-it notes',
      tasks: [
        { id: 1, name: 'Learn Vue JS', finished: false },
        { id: 2, name: 'Build a Vue application', finished: false },
        { id: 3, name: 'Write an article about Vue JS', finished: false }
      ]
    }
  },
  methods: {
    addTask() {
      if (this.newTask.length < 1) return
      
      this.tasks.push({
        id: this.tasks.length + 1,
        name: this.newTask,
        finished: false
      });

      this.newTask = ''
    },
    removeTask(taskID) {
      this.tasks = this.tasks.filter(task => {
        return task.id !== taskID
      });
    },
    finishTask(task) {
      task.finished = !task.finished
    }
  },

  computed: {
    allTasks() {
      return this.tasks.length
    },
    latest() {
      return [...this.tasks].reverse()
    }
  }
  
}
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
  font-size: 1.5em;
}

.strikeout {
  text-decoration: line-through;
}



body {background-color: rgb(206, 250, 250);}
h1 {background-color: rgb(172, 243, 243); text-align: center;}
h2 {background-color: rgb(158, 230, 230);}
h3 {background-color: rgb(133, 207, 207);}
img {float: right; border: 16px rgb(102, 206, 206) solid}
</style>
