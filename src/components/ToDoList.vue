<template>
  <div class="app">
    <ul style="list-style-type:none;">
      <li class="listItem" v-for="todo in todos" v-bind:key="todo.id">
        <div class="divTask" v-bind:class="getClass(todo.limitDate)">
          <span>Task: {{ todo.title }}</span>
          <span>Description: {{ todo.description }}</span>
          <span>Limit Date: {{ todo.limitDate }}</span>
          <div>
            <span>Is Complete: </span>
            <input type="checkbox" id="checkbox" v-model="todo.isComplete" v-on:change="changeComplete(todo.id, todo.isComplete)">
            <label for="checkbox">{{todo.isComplete}}</label>
          </div>
          <button v-on:click="addTodo(todo.title,todo.description,todo.limitDate)" >Clone Task</button>
          <button v-on:click="delTodo(todo.id)">Delete Task</button>
        </div>
      </li>
    </ul>
    <div class="inputs">
      <input placeholder="Todo" v-model="title" />
      <input placeholder="Description" v-model="description" />
      <input type="date" v-model="limitDate" />
      <div class="buttons">
        <button v-on:click="addTodo(title,description,limitDate)">Create Task</button>
      </div>
    </div>
  </div>
</template>


<script>
import axios from 'axios'
let url = 'http://[::1]:3000/todolist'

export default {
  data() {
    return {
      todos: [],
      today: new Date().toDateString()
    };
  },
  methods: {
    async addTodo(title,description,limitDate) {

      if (limitDate !== undefined) {
          await axios.post(url, {
            "title": title,
            "description": description,
            "limitDate": limitDate,
          })
        } else {
          await axios.post(url, {
            "title": title,
            "description": description,
          })
        }
        this.todos = await axios.get(url).then(res => res.data)
    },
    async delTodo(id) {
      let deleteLink = url + `/${id}`
      await axios.delete(deleteLink)
      this.todos = await axios.get(url).then(res => res.data)
    },
    getClass(limitDate){
      let testDate = new Date(limitDate).toDateString()
      if (testDate >= this.today) {
        return 'todoInDay'
      } else {
        return 'todoNotInDay'
      }
    },
    async changeComplete(id, state) {
      let changeLink = url + `/${id}`
      await axios.patch(changeLink, {"isComplete": state})
    }
  },
  mounted: async function(){
    this.todos = await axios.get(url).then(res => res.data)
    console.log(this.today)
  }
};
</script>

<style>
  span::after {
    content: " ";
  }
  span:last-child::after {
    content: "";
  }
  .todoInDay {
    background-color: lightgreen;
  }
  .todoNotInDay {
    background-color: orangered;
  }
  .inputs {
    display: flex;
    place-items: center;
    flex-direction: column;
  }
  .inputs input {
    width: 75%;
    margin-top: 1rem;
  }
  .buttons {
    margin-top: 1rem;
    text-align: center;
    width: 100%;
  }
  .divTask {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .divTask > *{
    margin: 0.175rem 0.125rem;
  }
  .listItem{
    margin-top: 0.215rem;
    padding: 0.135rem;
  }

</style>