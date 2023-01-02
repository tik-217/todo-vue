<template>
  <div class="todos_todoList">
    <div class="todo" v-for="todo in todosList">
      <input type="text" v-bind:value="todo" @:keydown="updateTodo($event)" @:dblclick="updateTodoAccess($event)"
        readonly>
      <div class="todo_delete" @:click="deleteTodo($event)"></div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    todosList: {
      type: Array
    },
    deleteTodo: {
      type: Function
    },
    updateTodo: {
      type: Function
    },
    updateTodoAccess: {
      type: Function
    }
  },
}
</script>

<style>
.todos_todosList {
  display: flex;
  flex-direction: column;
  margin-top: 30px;
}

.todo {
  position: relative;
}

input {
  border: 2px solid rgb(95, 115, 205);
  padding: 10px 15px;
  border-radius: 10px;
  box-sizing: border-box;
}

.todo input {
  margin-top: 10px;
  width: 100%;
  user-select: none;
}

.inputActive {
  /* border: 2px solid #ffc107 !important; */
  background-color: #c4cef8;
}

.todo_delete {
  position: absolute;
  top: 0;
  bottom: auto;
  right: 0;
  margin: 19px auto;
  cursor: pointer;
}

.todo_delete::before {
  content: "+";
  font-size: 20px;
  font-weight: 700;
  transform: rotate(45deg);
  position: absolute;
  z-index: 1;
  right: 0;
  top: 0;
  bottom: 0;
}
</style>