<template>
  <div id="app">
    <current-time class="col-4" />
    <task-input class="col-6" @add-task='addNewTask' />
    <div class="col-12">
      <div class="cardBox">
        <div class="container">
          <h2>My tasks</h2>
          <hr />

          <div class="col-4">
            <input v-model="hideDone" type="checkbox" id="hideDone" name="hideDone">
            <label for="hideDone">Hide Done Tasks</label>
          </div>
          <div class="col-4">
            <input v-model="reverse" type="checkbox" id="reverse" name="reverse">
            <label for="reverse">Reverse list order</label>
          </div>
          <div class="col-4">
            <input v-model="sortById" type="checkbox" id="sortById" name="sortById">
            <label for="sortById">sort by ID</label>
          </div>
          <ul class="taskList">
            <li v-for='(taskItem, index) in displayList' :key='`${index}_${Math.random()}`'
              :class="!!taskItem.finishedAt ? 'taskDone' : ''">
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
    hideDone: false,
    reverse: false,
    sortById: false,
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
      return this.hideDone
        ? [...this.baseList].filter(t => !t.finishedAt)
        : [...this.baseList];
    },
    // control order type
    sortedList() {
      return [...this.filterList]
        .sort((a, b) => (
          this.sortById
            ? b.id - a.id
            : (a.finishedAt || 0) - (b.finishedAt || 0)));
    },

    displayList() {
      const list = this.sortedList;

      return this.reverse
        ? list.reverse()
        : list;
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
  list-style: none;
  text-align: left;
  padding: 5px 10px;
  border-bottom: 1px solid rgba(0, 0 green, 0 blue, 0.15alpha);
}

.taskList li:last-child {
  border-bottom: 0px;
}

.taskList li:nth-child(even) {
  background-color: rgba(0, 0, 0, 0.15);
}

@keyframes colorChange {
  from {
    background-color: inherit;
  }

  to {
    background-color: rgba(0, 160, 24, 0.577);
  }
}

.taskList li.taskDone {
  animation: colorChange 1s ease;
  background-color: rgba(0, 160, 24, 0.577);
}
</style>