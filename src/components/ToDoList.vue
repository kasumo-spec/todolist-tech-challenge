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
        <div class="divTask">
          <div class="titleJob">
            <span>Task: {{ todo.title }}</span>
            <div class="tag" v-bind:class="getClass(todo.limitDate)"></div>
          </div>
          <span>Description: {{ todo.description }}</span>
          <span v-if="todo.limitDate">Limit Date: {{ todo.limitDate }}</span>
          <span v-else>Limit Date: Without date.</span>
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
      today: new Date().toLocaleDateString()
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
      let testDate = new Date(limitDate).toLocaleDateString()
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
    width: 46%;
    display: flex;
    margin-left: 27%;
    place-items: center;
    flex-direction: column;
    justify-content: center;
  }
  .inputs input {
    width: 21rem;
    border: none;
    margin-top: 1rem;
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
    padding: 0 0.175rem;
    align-items: center;
    flex-direction: column;
    background-color: lightgrey;
    border-radius: 0.225rem;
  }
  .divTask > *{
    margin: 0.175rem 0.125rem;
  }
  .list{
    display: flex;
    flex-wrap: wrap;
    width: 95vw;
    justify-content: space-evenly;
  }
  .listItem{
    min-width: 20%;
    max-width: 21%;
    padding: 0.135rem;
    margin-top: 0.215rem;
  }
  button {
    color: #fff;
    width: 6rem;
    border: none;
    margin: 0.5rem;
    cursor: pointer;
    padding: 0.32rem;
    border-radius: 0.5rem;
  }
  .buttonCreate{
    color: black;
    background-color: #18F2B2;
  }
  .buttonClone{
    background-color: #1C3041;
  }
  .buttonDelete{
    background-color: rgba(255, 0, 0, 0.69);
  }
  .tag{
    width: 0.575rem;
    height: 0.575rem;
    border-radius: 50%;
    margin-right: 0.575rem;
  }
  .titleJob{
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .isComplete{
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>