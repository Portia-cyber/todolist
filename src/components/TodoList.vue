<template>
  <div>
      <input type="text" class="todo-input" placeholder="what needs to be done " v-model="newTodo" @keyup.enter="addTodo">
     <transition-group name="fade" enter-active-class="animated fadeOutUp" leave-active-class="animated fadeOutDown">
      <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
        <div class="todo-item-left">
          <input type="checkbox" v-model="todo.completed">
          <div v-if="!todo.editing" @dblclick="editTodo(todo)" :class="{completed : todo.completed}" class="todo-item-label">
            {{todo.title}}
          </div>
          <input v-else type="text" class="todo-item-edit" v-model="todo.title" @keyup.esc="cancelEdit" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" v-focus>
        </div>
        <div class="remove-item" @click="removeTodo(index)">
          &times;
        </div>
      </div>
     </transition-group>
    <div class="extra-container">
      <div>
        <label>
          <input type="checkbox" :checked="!anyRemaining"
          @change="checkAllTodos">Check All
        </label>
      </div>
      <div>
        {{ remaining }} items left
      </div>
    </div>
    <div class="extra-container">
      <div>
        <button :class="{ active :filter == 'all'}" @click="filter ='all'">All</button>
        <button :class="{ active: filter == 'active'}" @click="filter ='active'">Active</button>
        <button :class=" { active: filter == 'completed'}" @click="filter =' completed'">Completed</button>
      </div>
      <div>
        <transition name="fade">
          <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          id: 1,
          title: 'Finish Vue',
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: 'Take Vue',
          completed: false,
          editing: false
        }
      ]
    }
  },
  computed: {
    remaining () {
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining () {
      // eslint-disable-next-line eqeqeq
      return this.remaining != 0
    },
    todosFiltered () {
      // eslint-disable-next-line eqeqeq
      if (this.filter == 'all') {
        return this.todos
        // eslint-disable-next-line eqeqeq
      } else if (this.filter == 'active') {
        return this.todos.filter(todo => !todo.completed)
        // eslint-disable-next-line eqeqeq
      } else if (this.filter == 'completed') {
        return this.todos.filter(todo => todo.completed)
      }
      return this.todos
    },
    showClearCompletedButton () {
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
    addTodo () {
      // eslint-disable-next-line eqeqeq
      if (this.newTodo.trim().length == 0) {
        return {
        }
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false
      })
      this.newTodo = ''
      this.idForTodo++
    },
    editTodo (todo) {
      this.beforeEditCache = todo.title
      todo.editing = true
    },
    doneEdit (todo) {
      todo.editing = false
      // eslint-disable-next-line eqeqeq
      if (todo.title.trim() == '') {
        todo.title = this.beforeEditCache
      }
    },
    cancelEdit (todo) {
      todo.title = this.beforeEditCache
      todo.editing = false
    },
    removeTodo (index) {
      this.todos.splice(index, 1)
    },
    checkAllTodos () {
      // eslint-disable-next-line no-return-assign
      this.todos.forEach((todo) => todo.completed = event.target.checked)
    },
    clearCompleted () {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script>

<style lang="scss">
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;

  &:focus {
    outline: 0;
  }
}
.todo-item{
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.remove-item{
  cursor: pointer;
  margin-left: 14px;
  &:hover{
    color: black;
  }
}
.todo-item-left{
  display: flex;
  align-items: center;
}
.todo-item-label{
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}
.todo-item-edit{
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  padding: 10px;
  width: 100%;
  border: 1px solid #ccc;
  font-family: 'Avenir',Helvetica,Arial,sans-serif;
  &:focus{
    outline: none;
  }
}
.completed{
  text-decoration: line-through ;
  color: grey;
}
.extra-container{
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}
button{
  font-size: 14px;
  background-color: white;
  appearance: none;
  &:hover{
    background: lightgreen;
  }
  &:focus{
    outline: none;
  }
}
.active{
  background: lightgreen;
}
.fade-enter-active, .fade-leave-active{
  transition: opacity .2s;
}
.fade-enter, .fade-leave-to{
  opacity: 0;
}
</style>
