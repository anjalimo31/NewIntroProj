<template>
  <div id="appContainer">
    
    <h1> {{ title }} </h1>
    <br>
    <h2>Add a new task</h2>
    

    <div class = "task-input">
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
      <h5>New task preview</h5>
      <p>{{ newTask }}</p>
    </div>
    <h6>Click on task for option to delete</h6>
    <ul class="task-list">
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
  </div>
</template>

<script>

export default {
  data() {
    return {
      title: 'My To Do App',
      newTask: '',
      tasks: []
    }
  },
  async created() {
    const response = await fetch("https://jgubwhhf94.execute-api.us-east-2.amazonaws.com/todos")
    this.tasks = await response.json()
  },
  methods: {
    async addTask() {
      if (this.newTask.length < 1) return
      let newtask = {
        id: Math.floor(Math.random() * (999999999 - 100000000) + 100000000),
        name: this.newTask,
        finished: false
      }
      await fetch("https://jgubwhhf94.execute-api.us-east-2.amazonaws.com/todos", {
        method: "post",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify(newtask)
      })
      this.tasks.push(newtask);

      this.newTask = ''
    },
    async removeTask(taskID) {
      await fetch("https://jgubwhhf94.execute-api.us-east-2.amazonaws.com/todos", {
        method: "delete",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({ id: taskID })
      })
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
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  font-size: 1.5em;
}

#appContainer {
  background-color: #ffffff;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 600px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

h1 {
  color: #333;
  font-size: 2.5rem;
  text-align: center;
}

h2 {
  color: #666;
  font-size: 1.5rem;
  margin-top: 1rem;
}


.task-input {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-top: 1rem;
}

.task-input span {
  color: #777;
  font-size: 0.9rem;
}

.task-input input {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

.task-input button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  background-color: #007bff;
  color: #fff;
  font-size: 1rem;
  cursor: pointer;
}

.task-input button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.task-preview {
  margin-top: 1rem;
  background-color: #f9f9f9;
  padding: 0.5rem;
  border-left: 4px solid #007bff;
}

.task-preview h5 {
  margin: 0;
  color: #007bff;
}

.task-preview p {
  margin: 0.5rem 0 0;
  color: #333;
}

.task-list {
  list-style: none;
  padding: 0;
  margin-top: 1rem;
}

.task-list li {
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.task-list li:last-child {
  border-bottom: none;
}

.task-list .task-item {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.task-list button {
  padding: 0.3rem 0.6rem;
  border: none;
  border-radius: 4px;
  background-color: #dc3545;
  color: #fff;
  font-size: 0.8rem;
  cursor: pointer;
}

.strikeout {
  text-decoration: line-through;
  color: #777;
}

.strikeout .task-item {
  opacity: 0.6;
}
</style>