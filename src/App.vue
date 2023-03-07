<template>
  <div id="todoapp">
    <current-time class="col-4"/>
    <task-input class="col-6" @add-task="'addNewTask'"/>
  </div>
</template>

<script>
import CurrentTime from './components/CurrentTime.vue';
import TaskInput from './components/TaskInput.vue';

export default {
  name: 'App',
  components: {
    CurrentTime,
    TaskInput,
  },
  methods: {
    addNewTask() {
      alert('New task added: ${task}');
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
