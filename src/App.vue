<template>
<section class="container debug-grid-18-solid">
  <div>
    <header>
     <h1 class="title">Things to do </h1>
     <input v-model="currentTodo" @keydown.enter="addTodo()" placeholder="Add a todo">
    </header>


   <ul class="todos">
      <li v-for="todo in filteredTodos" 
      :key="todo.id"
      :class="{ todo: true, editing: editedTodo === todo, completed: todo.completed}"
          @dblclick="editTodo(todo)"
          >
          <div class="view">
           
            <div class="title">
             <input class="check" type="checkbox" v-model="todo.completed" />
            {{ todo.label }}
            <button @click="removeTodo(todo)">&times;</button>
            </div>
          </div>
          <br />
          <input
            class="edit"
            v-model="todo.label"
            v-todo-html-focus="todo == editedTodo"
            @blur="cancelEdit(todo)"
            @keyup.esc="cancelEdit(todo)"
            @keyup.enter="doneEdit(todo)"
           />
           <br/>
           <br/>
           <br/>
      </li>
         <div class="items-left">{{completed}} items left</div>
      <div class="static">
        <button class="downButton" @click="visibility = 'all'" :class="{'items-all': true, selected: visibility === 'all'}">All</button>
        <button class="downButton" @click="visibility = 'active'" :class="{'items-active': true, selected: visibility === 'active'}">Active</button>
        <button class="downButton" @click="visibility = 'completed'" :class="{'items-completed': true, selected: visibility === 'completed'}">Completed</button>
      </div>
      <button class="items-clear-completed" v-if="completed !== todos.length" @click="removeCompleted">Clear completed</button>
    </ul>

   </div>
  </section>
</template>

<script>

import Vue from "vue";

let filters = {
  all: function(todos) {
    return todos;
  },
  active: function(todos) {
    return todos.filter(todo => !todo.completed);
  },
  completed: function(todos) {
    return todos.filter(todo => todo.completed);
  }
}


export default Vue.extend( {
  data() {
    return {
      todos: [],
      currentTodo: '',
      editedTodo: null,
      originalEditedTodoValue: "",
      visibility: 'all'
    };
  },
  methods: {
    addTodo() {
      this.todos.push({id: this.todos.length, label: this.currentTodo, completed: false});
      this.currentTodo = '';
    },
    removeTodo(todo) {
      var index = this.todos.indexOf(todo);
      this.todos.splice(index, 1);
    },
    removeCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    },
    editTodo(todo) {
      console.log("editTodo");
      // todo.completed = !todo.completed;
      this.editedTodo = todo;
      this.originalEditedTodoValue = todo.label;
    },
    doneEdit(todo) {
      console.log("doneEdit");
      this.editedTodo = null;
      if (!todo.label) {
        this.removeTodo(todo);
      }
    },
    cancelEdit(todo) {
      console.log("cancelEdit");
      if (!this.editedTodo) {
        return;
      }
      this.editedTodo = null;
      todo.label = this.originalEditedTodoValue;
    },
  },
  computed: {
    completed() {
      let remaining = 0;
      this.todos.forEach(todo => {
        if (!todo.completed) {
          remaining++;
        }
      });
      return remaining;
    },
    filteredTodos() {
      return filters[this.visibility](this.todos); 
    }
  },
   directives: {
    "todo-html-focus": function(el, binding) {
      if (binding.value) {
        el.focus();
      }
    }
  },
});
</script>

<style>
<style lang="scss">
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.debug * {
  outline: 1px solid gold;
}

body {
  display: flex;
  justify-content: center;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}
$spacer: 0.4em;
.container {
  display: flex;
  
  min-width: 70vw;
  margin-top: 80px;
 
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);
  input {
    padding: 0.6em;
  }
  header {
    h1 {
      position: absolute;
      top: 0;
      width: 70vw;
      font-size: 2em;
      text-align: center;
      text-transform: uppercase;
    }
    input {
      width: 100%;
      font-size: 1.5em;
      border: none;
      margin-top: .5em;
      padding: 1em;
    }
  }
  .todos {
    padding: 0;
    margin-top: 2em;
    margin-items: 3em;
    li {
      list-style-type: none;
       margin-top: 2em;
      .view {
        display: inline-block;
        margin-top: 1em;
        padding: 1em;
      
        .check {
          width: 16px;
          margin: .5em;
          padding: 1em;
          display: inline-box;
          align: left;
        }
        .title {
          flex-grow: 1;
          margin-top: 1em;
          padding: 1em;
        }
       
      }
      .edit {
        display: none;
        width: calc(100% - 16px - 0.4em);
        margin-left: calc(16px + 0.4em);
        margin-top: 1em;
        font-size: 1.5em;
      }
    }
    li.editing .view {
      display: none;
    }
    li.editing .edit {
      display: block;
    }
    li.completed .title {
      text-decoration: line-through;
    }
  }
  
    .static {
      flex: 1 1;
      display: flex;
      justify-content: center;
      div {
        padding: 0.1em $spacer;
        cursor: pointer;
        margin-top: 1em;
      }
      .selected {
        border: 1px dashed rgb(109, 109, 109);
        border-radius: 3px;
      }
    }
    
  }

.items-clear-completed {
      cursor: pointer;
       display: inline-block; 
   margin: 1em ;
   border: .094em solid #743EA5; 
   border-radius: .06em;
   background-color: #696969;
   box-shadow: 0 .05em .04em -0.04em rgba(0,0,0,.8), inset 0 .063em rgba(255,255,255,.4), inset 0 -0.188em rgba(0,0,0,.15);
   color: #FFF;
   text-decoration: none;
   text-transform: uppercase;
   text-shadow: 0 .063em #000;
   cursor:pointer;
   font: bold  sans-serif;
    }


ul{
  list-style: none;
  display: inline-block;
  margin-items: 3em;
}

.container{
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  font-size: 2em;
  }
  .downButton {
   display: inline-block; 
   margin: 1em ;
   border: .094em solid #743EA5; 
   border-radius: .06em;
   background-color: #DC143C;
   box-shadow: 0 .05em .04em -0.04em rgba(0,0,0,.8), inset 0 .063em rgba(255,255,255,.4), inset 0 -0.188em rgba(0,0,0,.15);
   color: #FFF;
   text-decoration: none;
   text-transform: uppercase;
   text-shadow: 0 .063em #000;
   cursor:pointer;
   font: bold  sans-serif;
}
.downButton:hover {
   background-color: #DC143C;
   box-shadow: 0 .125em rgba(255,255,255,.4), inset 0 .063em rgba(255,255, 255,.4), inset 0 -0.188em #804FAD;
}
.downButton:active {
   box-shadow: inset 0 0 1.094em #472566, inset 0 .063em #390668, inset 0 -0.188em #682CA0, 0 .063em rgba(255,255,255,.4); 
}


.items-left {
   
   margin: 2em ;
  
   
   text-decoration: none;
   text-transform: uppercase;
   
   
}

button {
          outline: none;
          border: 1px solid ;
          border-radius: 10%;
          padding: 1px 6px;
          margin: .3em;
          color: #000000;
          background-color: #e7e7e7;
        }
</style>
