<template>
  <div class="todos">
    <div class="todos_input" @:keydown="createTodo($event)">
      <transition name="fade">
        <p v-if="isError">Такая задача существует</p>
      </transition>
      <transition name="fade">
        <p v-if="isEmptyTask">Нельзя добавлять пустую задачу</p>
      </transition>
      
      <input type="text" @:input="inputTodo($event)" :value="todoInput" placeholder="Введите задачу">
    </div>
    <hr />
    <TodoList :todosList="todosList" :deleteTodo="deleteTodo" :updateTodo="updateTodo"
      :updateTodoAccess="updateTodoAccess" />
    <p v-if="todosList.length === 0">Задач нет</p>
  </div>
</template>

<script>
import TodoList from "./TodoList.vue";

export default {
  data() {
    return {
      todoInput: "",
      todoOldString: "",
      todosList: [],
      isReadonly: true,
      isError: false,
      isChangeTodosList: false,
      isEmptyTask: false,
    }
  },
  components: {
    TodoList
  },
  methods: {
    inputTodo(e) {
      const task = e.srcElement.value.trim();

      if (task.length !== 0) {
        this.todoInput = task;
      }
    },
    createTodo(e) {
      if (e.code !== "Enter") return;

      if (this.todoInput === "") {
        this.isEmptyTask = true;
        return;
      } else {
        this.isEmptyTask = false;
      }

      const filtered = this.todosList.filter((el) => el === this.todoInput);

      if (filtered.length === 0) {
        this.todosList = [this.todoInput, ...this.todosList];
        this.isError = false;
      } else {
        this.isError = true;
      };

      this.todoInput = "";
    },
    cancelChangeMode() {
      const todos = document.querySelectorAll(".todo");

      todos.forEach(el => {
        el.childNodes[0].classList.remove("inputActive");
        el.childNodes[0].setAttribute("readonly", "")
      });
    },
    updateTodoAccess(e) {
      this.cancelChangeMode();

      if (this.isReadonly) {
        e.target.classList.add("inputActive");
        e.target.removeAttribute("readonly");
      } else {
        e.target.classList.remove("inputActive");
        e.target.setAttribute("readonly", "");
      }

      this.todoOldString = e.target.value;
      this.isReadonly = !this.isReadonly;
    },
    updateTodo(e) {
      if (e.code !== "Enter") return;

      if (e.srcElement.value === "") {
        this.isEmptyTask = true;
        return;
      } else {
        this.isEmptyTask = false;
      }

      this.todosList = this.todosList.map((el) => {
        if (el === this.todoOldString) {
          el = e.srcElement.value;
        }
        return el;
      });

      this.updateTodoAccess(e);
    },
    deleteTodo(e) {
      const deleteInputValue = e.target.parentNode.children[0].value;

      this.todosList = this.todosList.filter(el => el !== deleteInputValue);
    },
    writeInLS() {
      localStorage.setItem("todosList", JSON.stringify(this.todosList));
    }
  },
  watch: {
    todosList(newTodosList, oldTodosList) {
      this.writeInLS();

      if (newTodosList.length !== oldTodosList.length) {
        this.isChangeTodosList = !this.isChangeTodosList;
      }
    }
  },
  mounted() {
    this.$nextTick(function () {
      const savedTodo = JSON.parse(localStorage.getItem("todosList"));
      this.todosList = [...savedTodo];
    })
  }
}
</script>

<style>
.todos {
  margin: 40px 30%;
}

.todos hr {
  margin-top: 20px;
  margin-bottom: 10px;
  background: #5f73cd;
  height: 2px;
  border: none;
}

.todos p {
  font-size: 14px;
  color: darkgrey;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity .5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>