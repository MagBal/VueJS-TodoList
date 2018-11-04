<template>
  <div>
      <input type="text" class ="todo-input" placeholder="add task" v-model="newTodo" v-on:keyup.enter="addTodo" />
      
      <transition-group name="fade" enter-active-class = "animated fadeInUp" leave-active-class="animated fadeOutDown">

        <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
          <div class="todo-item-left">

            <input type="checkbox" v-model="todo.completed">    
            <div v-if="!todo.editing" v-on:dblclick="editTodo(todo)" class="todo-item-label" :class="{completed : todo.completed}">
                {{todo.title}}
            </div>
            <input v-else class="todo-item-edit" type="text" v-model="todo.title" v-on:blur="doneEdit(todo)" v-on:keyup.enter="doneEdit(todo)" v-on:keyup.escape="cancelEdit(todo)" v-focus>
          </div>
          <div class="remove-items" v-on:click="removeTodo(index)">
              &#xe00e;
          </div>
        </div>
      </transition-group>

      <div class="extra-container">
        <div><label><input type="checkbox" :checked="!anyRemaining" v-on:change="checkAllTodos">Check All</label></div>
        <div>{{remaining}} items left</div>
      </div>

      <div class="extra-container">
        <div>
          <button :class="{active: filter == 'all'}" v-on:click="filter = 'all'">All</button>
          <button :class="{active: filter == 'active'}" v-on:click="filter = 'active'">Active</button>
          <button :class="{active: filter == 'completed'}" v-on:click="filter = 'completed'">Completed</button>
        </div>
      </div>
      <div>
        <transition name="fade">
          <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
        </transition>
      </div>
  </div>
</template>

<script>
export default {
  name: "todo-list",

  data() {
    return {
      newTodo: "",
      idForTodo: 3,
      beforeEditCache: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "Finish Vue Screencast",
          completed: false, //czy kompletna czy nie
          editing: false
        },
        {
          id: 2,
          title: "Take over world",
          completed: false,
          editing: false
        }
      ]
    };
  },

  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },
    anyRemaining() {
      return this.remaining != 0;
    },
    todosFiltered() {
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == "active") {
        return this.todos.filter(todo => !todo.completed);
      } else if (this.filter == "completed") {
        return this.todos.filter(todo => todo.completed);
      }
      return this.todos;
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0;
    }
  },

  directives: {
    focus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },

  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false
      });

      this.newTodo = ""; //zeby czyscilo inputa po wpisaniu taska
      this.idForTodo++; //zeby nadawalo id po kolei dla kazdego nowego taska
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },
    doneEdit(todo) {
      if (todo.title.trim() == "") {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    checkAllTodos() {
      this.todos.forEach(todo => (todo.completed = event.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    }
  }
};
</script>

<style>
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
}
.todo-input:focus {
  outline: 0;
}
.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.3s;
}
.remove-items {
  cursor: pointer;
  margin-left: 14px;
}
.remove-items:hover {
  color: #000;
}
.toto-item-left {
  display: flex;
  align-items: center;
}
.todo-item-label {
  padding: 10px;
  border: 1px solid #fff;
  margin-left: 12px;
}
.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  font-family: "Avenir", Helvetica, Arial, Helvetica, sans-serif;
}
.todo-input:focus {
  outline: none;
}
.completed {
  text-decoration: line-through;
  color: grey;
}
.extra-container {
  display: flex;
  align-content: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}
button {
  font-size: 14px;
  background-color: #fff;
  appearance: none;
}
button:hover {
  background: lightgreen;
}
button:focus {
  outline: none;
}
.active {
  background: lightgreen;
}
/* CSS Transitions */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
