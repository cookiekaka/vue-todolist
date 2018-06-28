<template>
<div class="todo-list">
  <div>
    <input class="todo-add" autofocus autocomplete="off"
      placeholder="请输入任务，并按回车键结束"
      v-model="todoText"
      @keyup.enter="addTodo" />
  </div>
  <div class="todo-main">
    <li class="todo-item" v-for="todo in todoList" :key="todo.id">
      <label class="todo-main-label">
        <input type="checkbox" v-model="todo.isDone" />
        <span class="todo-text">{{ todo.todoText }}</span>
      </label>
        <button class="remove-todo-btn" @click="removeTodo(todo)">删除</button>
    </li>
    <div class="no-todo" v-show="noDone">恭喜您已完成所有任务</div>
  </div>
  <div>
    <label class="todo-footer-label">
      <input class="all-done" type="checkbox" v-model="allDone" />
      <span>全选</span>
    </label>
    <span class="footer-info">
      <span class="done-count">已完成{{ doneCount }}</span>
      <span>/全部{{ allCount }}</span>
    </span>
    <button class="clear-done" @click="clearDone">清除已完成任务</button>
  </div>
</div>
</template>

<script>
// localStorage persistence
let STORAGE_KEY = 'todoList-vuejs-2.0';
let todoStorage = {
  fetch: function () {
    let todoList = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
    todoList.forEach((todo, index) => {
      todo.id = index;
    }) 
    todoStorage.uid = todoList.length;
    return todoList;
  },
  save: function (todoList) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todoList));
  }
}

export default {
  name: 'todolist',
  data () {
    return {
      todoList: todoStorage.fetch(),
      todoText: '',
    }
  },

  watch: {
    todoList: {
      handler: function (todoList) {
        console.log(todoList);
        todoStorage.save(todoList);
      },
      deep: true
    }
  },

  computed: {
    allDone: {
      get: function () {
        return this.todoList.length !== 0 && this.todoList.every((todo) => todo.isDone);
      },
      set: function (value) {
        this.todoList.forEach((todo) => {
          todo.isDone = value;
        })
      }
    },
    doneCount: function () {
      return this.todoList.filter((todo) => todo.isDone).length;
    },
      
    allCount: function () {
      return this.todoList.length;
    },
    noDone: function () {
      return this.todoList.length === 0;
    }
  },

  methods: {
    addTodo: function () {
      var value = this.todoText && this.todoText.trim();
      if (!value) {
        return;
      }
      this.todoList.push({
        id: todoStorage.uid++,
        todoText: value,
        isDone: false
      })
      this.todoText = '';
    },
    removeTodo: function (todo) {
      this.todoList.splice(this.todoList.indexOf(todo), 1);
    },
    clearDone: function () {
      this.todoList = this.todoList.filter((todo) => !todo.isDone);
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
.remove-todo-btn, .clear-done {
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
.todo-add {
  width: 480px;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 3px;
  margin-bottom: 10px;
  font-size: 14px;
}
.todo-item {
  float: left;
  width: 480px;
  margin-left: 4px;
  padding: 0 5px;
  border: 1px solid #ccc;
  border-bottom: none;
  list-style-type: none;
}
.todo-item:last-of-type {
  border-bottom: 1px solid #ccc;
  margin-bottom: 8px;
}
.todo-main-label {
  float: left;
}
.todo-main input:checked+span {
  text-decoration: line-through;
}
.todo-text {
  vertical-align: middle;
  margin-left: 5px;
}
.todo-footer-label {
  float: left;
  margin-left: 10px;
}
.all-done {
  margin-right: 9px;
}
.footer-info {
  float: left;
  margin-left: 20px;
}
.done-count {
  color: rgb(25, 161, 25);
}
.clear-done {
  margin-right: 10px;
}
.no-todo {
  width: 490px;
  border: 1px solid #ccc;
  margin: 0 auto 8px auto;
  text-align: center;
}
</style>
