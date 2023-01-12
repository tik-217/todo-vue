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

<script lang="ts">
// vue
import { defineComponent } from "vue";

// components
import TodoList from "./TodoList.vue";

// types
import { Todo } from "../../types";

export default defineComponent({
  data() {
    return {
      todoInput: "",
      todoOldString: "",
      todosList: [],
      isReadonly: true,
      isError: false,
      isChangeTodosList: false,
      isEmptyTask: false,
    } as Todo
  },
  components: {
    TodoList
  },
  methods: {
    inputTodo(e: Event) {
      const task = (e.target as HTMLInputElement).value.trim();

      if (task.length !== 0) {
        this.todoInput = task;
      }
    },
    createTodo(e: KeyboardEvent) {
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

      todos.forEach((el) => {
        (el.childNodes[0] as HTMLInputElement).classList.remove("inputActive");
        (el.childNodes[0] as HTMLInputElement).setAttribute("readonly", "");
      });
    },
    updateTodoAccess(e: Event) {
      this.cancelChangeMode();

      const taskInput = e.target as HTMLInputElement;

      if (this.isReadonly) {
        taskInput.classList.add("inputActive");
        taskInput.removeAttribute("readonly");
      } else {
        taskInput.classList.remove("inputActive");
        taskInput.setAttribute("readonly", "");
      }

      this.todoOldString = taskInput.value;
      this.isReadonly = !this.isReadonly;
    },
    updateTodo(e: KeyboardEvent) {
      if (e.code !== "Enter") return;

      if ((e.target as HTMLInputElement).value === "") {
        this.isEmptyTask = true;
        return;
      } else {
        this.isEmptyTask = false;
      }

      this.todosList = this.todosList.map((el: string) => {
        if (el === this.todoOldString) {
          el = (e.target as HTMLInputElement).value;
        }
        return el;
      });

      this.updateTodoAccess(e);
    },
    deleteTodo(e: MouseEvent) {
      const parentNodeTask = (e.target as HTMLDivElement).parentNode as HTMLDivElement;
      const deleteInputValue = (parentNodeTask.children[0] as HTMLInputElement).value;

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
      const savedTodo = localStorage.getItem("todosList");
      const todosListLS = savedTodo && JSON.parse(savedTodo);
      this.todosList = [...todosListLS];
    });
  }
});
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