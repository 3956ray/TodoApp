<template>
  <div id="app">
    <current-time class="col-4" />
    <task-input class="col-6" @add-task='addNewTask' />
    <div class="col-12">
      <div class="cardBox">
        <div class="container">
          <h2>My tasks</h2>
          <ul class="taskList">
            <li v-for='(taskItem, index) in displayList' :key='`${index}_${Math.random()}`'>
              <input type="checkbox" :checked='!!taskItem.finishedAt' @input="changeStatus(taskItem.id)" />

              # {{ taskItem.id }} - {{ taskItem.task }}
              <span v-if='taskItem.finishedAt'>Done at {{ formatDate(taskItem.finishedAt) }}</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CurrentTime from './components/CurrentTime.vue';
import TaskInput from './components/TaskInput.vue';

export default {
  name: 'TodoApp',
  components: {
    CurrentTime,
    TaskInput,
  },

  data: () => ({
    taskList: [],
  }),

  // pass the calue to the template
  // value is cached so we don't worry value can be changed in template.
  // not waste processing time re-rendering DOM tree
  computed: {

    // new array with same task
    // add new "id" property to the task
    baseList() {
      return [...this.taskList]
        .map((t, index) => ({
          ...t,
          id: index + 1
        }));
    },
    // 
    filterList() {
      return [...this.baseList].filter(t => !t.finishedAt);
    },
    // ascending order
    sortedList() {
      return [...this.filterList].sort((a, b) => a.id - b.id);
    },

    displayList() {
      return this.sortedList;
    },
  },

  methods: {
    formatDate(value) {
      if (!value)
        return '';
      if (typeof value !== 'number')
        return value;
      const browserLocale =
        navigator.languages && navigator.languages.length
          ? navigator.languages[0]
          : navigator.language;

      const intlDateTime = new Intl.DateTimeFormat(browserLocale, {
        year: "numeric",
        month: "numeric",
        day: "numeric",
        hour: "numeric",
        minute: "numeric",
      });
      return intlDateTime.format(new Date());
    },

    addNewTask(task) {
      this.taskList.push({
        task,
        createdAt: Date.now(),
        finishedAt: undefined,
      })
    },

    changeStatus(taskID) {
      const task = this.taskList[taskID - 1];
      if (task.finishedAt) {
        task.finishedAt = undefined;
      } else {
        task.finishedAt = Date.now();
      }
    },
  },
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

<style scoped>
.taskList li {
  text-align: left;
}
</style>