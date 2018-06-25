<template>
<div class="todo-list">
  <div class="todo-header">
    <input class="todo-header-input" autofocus autocomplete="off"
      placeholder="请输入任务，并按回车键结束"
      v-model="todoText"
      @keyup.enter="addTodo" />
  </div>
  <div class="todo-main">
    <li class="todo-main-li" v-for="todo in todos" :key="todo.id">
      <label class="todo-main-label">
        <input calss="todo-main-input" type="checkbox" v-model="todo.isDone" />
        <span class="todo-main-label-span">{{ todo.todoText }}</span>
      </label>
        <button class="todo-list-button" @click="removeTodo(todo)">删除</button>
    </li>
  </div>
  <div class="todo-footer">
    <label class="todo-footer-label">
      <input class="todo-footer-input" type="checkbox" v-model="allDone" />
      <span>全选</span>
    </label>
    <span class="footer-info">
      <span class="done-count">已完成{{ doneCount }}</span>
      <span>/全部{{ allCount }}</span>
    </span>
    <button class="todo-footer-button" @click="clearDone">清除已完成任务</button>
  </div>
</div>
</template>

<script>
// localStorage persistence
let STORAGE_KEY = 'todos-vuejs-2.0';
let todoStorage = {
  fetch: function () {
    let todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
    todos.forEach((todo, index) => {
      todo.id = index;
    }) 
    todoStorage.uid = todos.length;
    return todos;
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
  }
}

export default {
  name: 'todolist',
  data () {
    return {
      todos: todoStorage.fetch(),
      todoText: '',
    }
  },

  watch: {
    todos: {
      handler: function (todos) {
        todoStorage.save(todos);
      },
      deep: true
    }
  },

  computed: {
    allDone: {
      get: function () {
        return this.todos.every((todo) => todo.isDone);
      },
      set: function (value) {
        this.todos.forEach((todo) => {
          todo.isDone = value;
        })
      }
    },
    doneCount: function () {
      return this.todos.filter((todo) => todo.isDone).length;
    },
      
    allCount: function () {
      return this.todos.length;
    },
  },

  methods: {
    addTodo: function () {
      var value = this.todoText && this.todoText.trim();
      if (!value) {
        return;
      }
      this.todos.push({
        id: todoStorage.uid++,
        todoText: value,
        isDone: false
      })
      this.todoText = '';
    },
    removeTodo: function (todo) {
      this.todos.splice(this.todos.indexOf(todo), 1);
    },
    clearDone: function () {
      this.todos = this.todos.filter((todo) => !todo.isDone);
    },
  }
}
</script>

<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
/* my style */
.todo-list {
  display: flow-root;
  width: 500px;
  margin: 10px auto 0 auto;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  line-height: 30px;
}
.todo-list-button, .todo-footer-button {
  float: right;
  margin-top: 5px;
  padding: 2px 8px;
  font-size: 12px;
  line-height: 16px;
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
  cursor: pointer;
}
.todo-header-input {
  width: 480px;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 3px;
  margin-bottom: 10px;
  font-size: 14px;
}
.todo-main-li {
  float: left;
  width: 480px;
  margin-left: 4px;
  padding: 0 5px;
  border: 1px solid #ccc;
  border-bottom: none;
  list-style-type: none;
}
.todo-main-li:last-of-type {
  border-bottom: 1px solid #ccc;
  margin-bottom: 8px;
}
.todo-main-label {
  float: left;
}
.todo-main input:checked+span {
  text-decoration: line-through;
}
.todo-main-label-span {
  vertical-align: middle;
  margin-left: 5px;
}
.todo-footer-label {
  float: left;
  margin-left: 10px;
}
.todo-footer-input {
  margin-right: 9px;
}
.footer-info {
  float: left;
  margin-left: 20px;
}
.done-count {
  color: rgb(25, 161, 25);
}
.todo-footer-button {
  margin-right: 10px;
}
</style>
