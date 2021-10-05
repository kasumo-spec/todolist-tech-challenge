<template>
  <div class="toDoList">
    <div class="inputs">
      <input placeholder="Todo" v-model="title" />
      <input placeholder="Description" v-model="description" />
      <input type="date" v-model="limitDate" />
      <div class="buttons">
        <button class="buttonCreate" v-on:click="addTodo(title,description,limitDate)">Create Task</button>
      </div>
    </div>
    <div class="list">
      <div class="listItem" v-for="todo in todos" v-bind:key="todo.id">
        <div class="divTask" v-bind:class="getClass(todo.limitDate)">
          <span>Task: {{ todo.title }}</span>
          <span>Description: {{ todo.description }}</span>
          <span>Limit Date: {{ todo.limitDate }}</span>
          <div class="isComplete">
            <span>Is Complete: </span>
            <input type="checkbox" id="checkbox" v-model="todo.isComplete" v-on:change="changeComplete(todo.id, todo.isComplete)">
            <label for="checkbox" v-if="todo.isComplete">True</label>
            <label for="checkbox" v-else>False</label>
          </div>
          <div class="buttons">
            <button class="buttonClone" v-on:click="addTodo(todo.title,todo.description,todo.limitDate)" >Clone Task</button>
            <button class="buttonDelete" v-on:click="delTodo(todo.id)">Delete Task</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import axios from 'axios'
let url = 'https://api-for-exati-tech-test.herokuapp.com/todolist'

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
    background-color: #18F2B2;
  }
  .todoNotInDay {
    background-color: #89043D;
  }
  .inputs {
    display: flex;
    place-items: center;
    justify-content: center;
    flex-direction: column;
    width: 46%;
    margin-left: 27%;
  }
  .inputs input {
    margin-top: 1rem;
    width: 21rem;
    border: none;
    border-radius: 1rem;
    background-color: #2FE6DE;
    padding: 0.175rem 0.175rem 0.175rem 1rem;
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
    border-radius: 0.225rem;
    padding: 0 0.175rem;
  }
  .divTask > *{
    margin: 0.175rem 0.125rem;
  }
  .list{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
  }
  .listItem{
    margin-top: 0.215rem;
    padding: 0.135rem;
    max-width: 21%;
  }
  button {
    width: 6rem;
    border: none;
    border-radius: 0.5rem;
    margin: 0.5rem;
    padding: 0.32rem;
    color: #fff;
    cursor: pointer;
  }
  .buttonCreate{
    background-color: #18F2B2;
    color: black;
  }
  .buttonClone{
    background-color: #1C3041;
  }
  .buttonDelete{
    background-color: rgba(255, 0, 0, 0.69);
  }
  .isComplete{
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>