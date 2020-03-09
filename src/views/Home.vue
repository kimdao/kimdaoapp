<template>
  <div class="home">
    <v-text-field v-model="keyword" label="Search" placeholder="Enter keyword..." clearable />
    <v-text-field v-model="addingTitle" label="Add Task" placeholder="Enter title..." @keydown.enter="addTask" />
    <v-btn @click="addTask" class="btnAdd">Add</v-btn>

    <h3>Task</h3>
    <h5 v-if="keyword">Result of searching with keyword {{keyword}} (Total: {{this.listTaskAfterSearch.length }}): </h5>
    <div class="listTask">
      <draggable handle=".not-done">
          <transition-group>
            <v-layout v-for="(task, tIdx) in listTaskNotDone" :key="`task-not-done-${tIdx}`" class="task" :class="task.status === 'done' ? 'task-done' : 'not-done'" align-center>
              <div>
                {{task.name}}
              </div>
              <v-spacer />
              <div>
                <v-btn v-if="task.status !== 'done'" @click="doneTask(task.id)" class="btnDone"><i class="fas fa-check"></i></v-btn>
                <v-btn @click="removeTask(task.id)" class="btnNotDone"><i class="fas fa-times"></i></v-btn>
              </div>
            </v-layout>
          </transition-group>
      </draggable>
      <v-layout v-for="(task, tIdx) in listTaskDone" :key="`task-done-${tIdx}`" class="task" :class="task.status === 'done' ? 'task-done' : 'not-done'" align-center>
        <div>
          {{task.name}}
        </div>
        <v-spacer />
        <div>
          <v-btn @click="removeTask(task.id)" class="btnNotDone"><i class="fas fa-times"></i></v-btn>
        </div>
      </v-layout>
    </div>
  </div>
</template>

<style lang="scss">
.v-application--wrap  {
  justify-content: center;
  align-items: center;
}
.home {
  width: 500px;
  height: auto;
  border: 3px solid #f1f1f1;
  padding: 12px 20px;
  .btnAdd{
    background-color: #2196f3 !important;
    color: white;
    padding: 14px 20px;
    margin: 8px 0;
    border: none;
    cursor: pointer;
    width: 100%;
  }
  .btnDone{
    background-color: #0ddb4bf5 !important;
    min-width: 35px !important;
  }
  .btnNotDone{
    background-color: #e2380df5 !important;
    min-width: 35px !important;
  }
  h3 {
    padding-bottom: 10px;
    border-bottom: 2px solid #cc9191;
    margin-bottom: 30px;
  }
  .listTask {
    background: #dff0d8;
    border-radius: 10px;
    padding: 0;
    overflow: hidden;
  }
  .task {
    padding: 15px 10px !important;
    margin: 0;
    border-bottom: 1px solid;
    &:last-child {
      border: none;
    }
  }
  .task-done {
    position: relative; 
    background: #d9edf7; 
    &:after {
      position: absolute;
      left: 10px;
      right: 10px;
      top: 50%;
      height: 2px;
      background: rgb(7, 6, 6);
      content: "";
      display: block;
    }
  }
}
</style>

<script>
import draggable from 'vuedraggable'

export default {
  name: 'Home',
  components: {
    draggable
  },
  computed: {
    listTaskAfterSearch () {
      if (this.keyword) {
        let reg = new RegExp(this.keyword, 'gi')
        return this.listTask.filter(taskObj => taskObj.name && String(taskObj.name).match(reg))
      }
      return this.listTask
    },
    listTaskNotDone () {
      return this.listTaskAfterSearch.filter(e => e.status !== 'done')
    },
    listTaskDone () {
      return this.listTaskAfterSearch.filter(e => e.status === 'done').sort((item1, item2) => {
        if (item1.updatedAt > item2.updatedAt) return 1
        return -1
      })
    },
  },
  data: () => ({
    listTask: [{
      id: 'id1',
      name: 'task1',
      status: 'not-done'
    }, {
      id: 'id2',
      name: 'task2',
      status: 'done',
      updatedAt: new Date().getTime()
    }],
    keyword: null,
    addingTitle: null
  }),
  methods: {
    generateId () {
      let id = 'id' + Math.floor(Math.random() * Math.floor(Number.MAX_SAFE_INTEGER))
      while (this.listTask.map(e => e.id).includes(id)) {
        id = 'id' + Math.floor(Math.random() * Math.floor(Number.MAX_SAFE_INTEGER))
      }
      return id
    },
    addTask () {
      if (!this.addingTitle) {
        alert('Title cannot empty!')
      } else {
        this.listTask.push({
          id: this.generateId(),
          status: 'not-done',
          name: this.addingTitle
        })
        this.addingTitle = null
      }
    },
    doneTask (id) {
      let index = this.listTask.findIndex(e => e.id === id)
      if (index !== -1) {
        this.listTask[index].status = 'done'
        this.listTask[index].updatedAt = new Date().getTime()
        this.listTask = [...this.listTask]
      }
    },
    removeTask (id) {
      let index = this.listTask.findIndex(e => e.id === id)
      if (index !== -1) {
        this.listTask.splice(index, 1)
      }
    }
  }
}
</script>
