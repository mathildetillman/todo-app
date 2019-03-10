<template>
    <div>
      <input type="text" class="todo-input"
             placeholder="What needs to be done?"
             v-model="newTodo" @keydown.enter="addTodo">
      <transition-group name="fade" enter-active-classs="animated fadeInUp"
         leave-active-class="animated fadeOutDown"  >
        <div v-for="(todo, index) in todosFiltered":key="todo.id"
             class="todo-item">
          <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed" >
            <div v-if="!todo.editing" class="todo-item-label"
                 @dblclick="editTodo(todo)" :class="{ completed: todo.completed }" >
              {{todo.title}}
            </div>
            <input v-else type="text" v-model="todo.title"
                   class="todo-item-edit"
                   @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)"
                   v-focus @keyup.esc="cancelEdit(todo)" >
          </div>
              <div class="remove-item" @click="removeTodo(index)">
                &times;
              </div>
        </div>
      </transition-group>

    <div class="extra-container">
      <div>
        <label><input type="checkbox" @change="checkAll()" :checked="!anyRemaining"/> Check all </label>
      </div>
      <div> {{remaining}} items left </div>
    </div>

      <div class="extra-container">
        <div>
          <button :class="{active : filter === 'all'}" @click="filter='all'"> All </button>
          <button :class="{active : filter === 'active'}" @click="filter='active'" > Active </button>
          <button :class="{active: filter ==='completed'}" @click="filter='completed'" > Completed </button>
        </div>
        <div>
          <transition name="fade">
            <button @click="clearCompleted" v-if="showClearCompleted" @blur="dontClearCompleted()"> Clear completed
              <v-icon small>
                delete
              </v-icon>
            </button>
          </transition>
        </div>
      </div>
      <div v-if="error !== ''" id="error" > {{error}} </div>
      <div class="bottom"></div>
    </div>
</template>

<script>
  export default {
    name: 'TodoList',
    data () {
      return {
       newTodo: '',
        beforeEditCache: '',
        idForTodo: 3,
        filter: 'all',
        confirmDelete: 0,
        error: '',
        todos: [
          {
            'id': 1,
            'title': "Finish todo app",
            'completed': false,
            'editing': false,
          },
          {
            'id': 2,
            'title': "Take over world",
            'completed': false,
            'editing': false,
          }
        ]
      }
    },
    computed: {
      remaining() {
        return this.todos.filter(todo => !todo.completed).length;
      },
      anyRemaining(){
        return this.remaining != 0;
      },
      todosFiltered(){
        if(this.filter == 'all'){
          return this.todos;
        }
        else if(this.filter == 'active'){
          return this.todos.filter(todo => !todo.completed);
        }
        else if(this.filter == 'completed'){
          return this.todos.filter(todo => todo.completed);
        }
        return this.todos;
      },
      showClearCompleted(){
        return this.todos.filter(todo => todo.completed).length > 0
      },
  },
    directives:{
      focus: {
        inserted: function(el){
          el.focus();
        }
      }
    },
    methods:{
      addTodo() {
        if(this.newTodo.trim().length === 0){
          return
        }

        this.todos.push({
          id: this.idForTodo,
          title: this.newTodo,
          completed: false,
          editing: false,
          }),
        this.newTodo = '',
        this.idForTodo++
      },
      removeTodo(index){
        this.todos.splice(index, 1)
      },
      editTodo(todo){
        this.beforeEditCache= todo.title;
        todo.editing = true;
      },
      cancelEdit(todo){
        todo.title=this.beforeEditCache;
        todo.editing = false;
      },
      doneEdit(todo){
        if(todo.title.trim().length === 0){
          this.cancelEdit(todo);
        }
        todo.editing = false;
      },
      checkAll(){
        this.todos.forEach((todo) => todo.completed = event.target.checked);
      },
      clearCompleted(){
        this.confirmDelete++;
        if (this.confirmDelete === 1){
          this.error = 'Press "Clear completed" again to confirm';
        }
        if(this.confirmDelete === 2){
          this.todos = this.todos.filter(todo => !todo.completed)
          this.confirmDelete = 0;
          this.error = '';
        }
      },
      dontClearCompleted(){
        this.error = '';
        this.confirmDelete = 0;
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
  @import"https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css";

  .todo-input{
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
    border: 2px green;
    border-bottom: 1px solid lightgrey;
  }

  .todo-item {
    display: flex;
    margin-bottom:12px;
    padding: 10px;
    align-items: center;
    justify-content: space-between;
  }

  .remove-item{
    cursor: pointer;
    margin-left: 14px;
    &:hover{
      color: #8b2d3b;
    }
  }

  .todo-item-left{
    display:flex;
    align-items: center;
  }

  .todo-item-label{
    padding: 10px;
    //border: 1px solid white;
    margin-left: 12px;
  }

  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    padding: 10px;
    width: 100%;
    border: 1px #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
  }

  .completed {
    text-decoration: line-through;
    color: grey;
  }

  .extra-container{
    font-size: 18px;
    color: grey;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-top: 1px solid lightgrey;
    margin-top: 10px;
    padding-top: 14px;
    padding-left: 10px;
    padding-right: 10px;

  }

  .active{
    background: #eed337;
  }

  button{
    font-size: 14px;
    background-color: white;
    border-radius: 4px;
    border: lightgrey;
    padding-top: 3px;
    padding-bottom: 3px;
    padding-left: 5px;
    padding-right: 5px;

    &:hover{
      background: #eed337
      //box-shadow: 0 8px 12px 0 rgba(0,0,0,0.15), 0 12px 20px 0 rgba(0,0,0,0.10);
    }

    &:focus{
      outline:none;
    }
  }

  .bottom {
    padding-bottom: 60px;
  }

  #error{
    font-size: 14px;
    padding: 15px;
    color: red;
    text-align: right;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
  }

  .fade-enter-active, .fade-leave-active{
    transition: opacity .6s;
  }

  .fade-enter, .fade-leave{
    opacity: 0;
  }

</style>
