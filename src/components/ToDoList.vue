<template>
  <div id="todo-list">
    <h1>To Do List</h1>
    <input 
      type="text" 
      class="todo-input" 
      placeholder="What needs to be done"
      v-model="newTodo"
      @keyup.enter="addNewToDo"
    >
    <transition-group
      name="fade"
      enter-active-class="animated fadeInUp"
      leave-active-class="animated fadeOutDown"
    >
      <div 
        v-for="(todo, index) in todosFiltered" 
        :key="todo.id" 
        class="todo-item"
      >
        <div class="todo-item-left">
          <input 
            type="checkbox"
            v-model="todo.completed"
          >
          <div 
            v-if="!todo.editing"
            @dblclick="editToDo(todo)"
            class="todo-item-lable"
            :class="{ completed : todo.completed }"
          >
            {{ todo.title }}
          </div>
          <input 
            type="text" 
            v-else
            v-model="todo.title"
            class="todo-item-edit"
            @blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            v-focus
            @keyup.esc="cancelEdit(todo)"
          >
        </div>
        <div 
          class="remove-item"
          @click="removeToDo(index)"
        >
          &times;
        </div>
      </div>
    </transition-group>

    <div class="extra-container">
      <div>
        <label>
          <input 
            type="checkbox"
            :checked="!anyRemaining"
            @change="checkAllTodos"
          > 
          Check All
        </label>
      </div>
      <div>
        {{ remaining }} items left
      </div>
    </div>

    <div class="extra-container">
      <div>
        <button
          :class="{ active: filter == 'all' }"
          @click="filter = 'all'"
        >
         All
        </button>
        <button
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
         Active
        </button>
        <button
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
         Completed
        </button>
      </div>

      <div>
        <transition 
          name="fade"
        >
        <button
          v-if="showClearCompletedButton"
          @click="clearCompleted"
        >
          Clear Completed
        </button> 
        </transition>
      </div>
    </div>

  </div>
</template>

<script>


export default {
  name: 'ToDoList',
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          id: 1,
          title: 'Finish the course',
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: 'To water flowers',
          completed: false,
          editing: false
        },
      ]
    }
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining() {
      return this.remaining != 0
    },
    todosFiltered() {
      if (this.filter == 'all') {
        return this.todos 
      } else if (this.filter == 'active') {
        return this.todos.filter(todo => !todo.completed)
      } else if (this.filter == 'completed') {
        return this.todos.filter(todo => todo.completed)
      }
      return this.todos
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },
  methods: {
    addNewToDo() {
      if (this.newTodo.trim().length == 0) {
        return
      }

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false
      })

      this.newTodo = ''
      this.idForTodo++
    },
    removeToDo(index) {
      this.todos.splice(index, 1)
    },
    editToDo(todo) {
      this.beforeEditCache = todo.title
      todo.editing = true
    },
    doneEdit(todo) {
      if (todo.title.trim() == '') {
        todo.title = this.beforeEditCache
      }
      todo.editing = false
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache
      todo.editing = false
    },
    checkAllTodos() {
      this.todos.forEach((todo) => todo.completed =
      event.target.checked)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script>

<style>
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");

  h2 {
    color: #474747;
    font-size: 2em;
  }

  .todo-input {
    width: 100%;
    font-size: 18px;
    margin-bottom: 15px;
    padding: 10px;
  }

  .todo-item {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    animation-duration: 0.4s;
  }

  .todo-item-left {
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
  }

  .todo-item-left input[type="checkbox"]{
    margin-right: 10px;
  }

  .remove-item {
    cursor: pointer;
    margin-left: 14px;  /* ? */
  }

  &:hover {
    color: black;
  }

  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Arial, Helvetica, sans-serif;
  }

  .completed {
    text-decoration: line-through;
    color: grey;
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  button {
    font-size: 14px;
    font-family: Avenir, Helvetica, Arial, sans-serif;
    background-color: white;
    appearance: none;
    margin-right: 5px;
    border: 1px solid grey;
    border-radius: 3px;
    padding: 2px 10px;
  }

  button:hover {
    background-color: #bdbdbd;
  }

  .active {
    background: #94b5e7;
  }

  /* // CSS Transition */
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity .2s;
  }

  .fade-enter,
  .fade-leave-to {
    opacity: 0;
  }

  @media (max-width:767px) {
    h2 {
      font-size: 1.1em;
    }

    .todo-input {
    font-size: .8em;
    }

    .todo-item-lable {
      font-size: 0.7em;
    }

    .extra-container {
      display: flex;
      flex-direction: column;
    }

    .extra-container button {
      margin-bottom: 15px;
    }
  }

</style>
