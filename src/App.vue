<template>
  <v-app>
    <v-toolbar dense dark color=#0066cc app>
      <v-toolbar-title >
        TODO App
      </v-toolbar-title>
    </v-toolbar>
    <div class="container">
      <img src="./assets/todo.png" class="logo">
      <v-content id="app">
        <Todo/>
      </v-content>
    </div>
  </v-app>
</template>

<script>
import Todo from './components/TodoList'

export default {
  name: 'App',
  components: {
    Todo
  },
  data () {
    return {
      //
    }
  }
}
</script>

<style>
  * {
    box-sizing: border-box;
  }

  .container{
    max-width: 600px;
    margin: 0 auto;
  }

  #app{
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin-top: 60px;
    font-size: 24px;
  }

  .toolbarTitle{
    font-size: 50px;
    display: flex;
    text-align: end;
    align-items: center;
  }

  .logo{
    display: block;
    margin-left: auto;
    margin-right: auto;
    width: 30%;
  }

</style>
