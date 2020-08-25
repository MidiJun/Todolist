<template>
  <q-page class="bg-grey-2 column">
    <div class="row bg-primary q-pa-sm">
      <q-input
        v-model="newTask"
        filled
        placeholder="想要做点什么…"
        dense
        square
        class="bg-white col"
        @keyup.enter="addTask"
      >
        <template>
          <q-btn round dense flat icon="add" @click="addTask" />
        </template>
      </q-input>
    </div>

    <q-list class="bg-white" v-if="tasks.length">
      <q-item
        v-for="(task, i) in tasks"
        :key="i"
        v-ripple
        clickable
        @click="taskStatus(task)"
        :class="{ 'done bg-teal-1': task.done }"
      >
        <q-item-section avatar>
          <q-checkbox
            v-model="task.done"
            color="primary"
            class="no-pointer-events"
          />
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ task.title }}</q-item-label>
        </q-item-section>
        <q-item-section side v-if="task.done" @click.stop="deleteTask(i)">
          <q-btn flat round color="primary" dense icon="delete" />
        </q-item-section>
      </q-item>
    </q-list>
    <div class="no-tasks absolute-center" v-if="!tasks.length">
      <q-icon name="check" size="100px" color="primary" />
      <div class="text-h5 text-primary text-center">No tasks</div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      newTask: "",
      list: [],
      tasks: []
      // tasks: [
      //   {
      //     title: "打扫卫生",
      //     done: false
      //   },
      //   {
      //     title: "整理文档",
      //     done: true
      //   }
      // ]
    };
  },
  created() {
    this.getTasks();
  },
  methods: {
    getTasks() {
      this.list = JSON.parse(localStorage.getItem("list") || "[]");
      if (this.list[this.id].tasks) {
        this.tasks = this.list[this.id].tasks;
      } else this.tasks = [];
    },
    taskStatus(task) {
      task.done = !task.done;
      this.list[this.id].tasks = this.tasks;
      localStorage.setItem("list", JSON.stringify(this.list));
    },
    deleteTask(index) {
      this.$q
        .dialog({
          title: "提示",
          message: "你要删除此任务吗?",
          cancel: true,
          persistent: true
        })
        .onOk(() => {
          this.tasks.splice(index, 1);
          this.$q.notify({
            message: "此任务已删除",
            icon: "done"
          });
          this.list[this.id].tasks = this.tasks;
          localStorage.setItem("list", JSON.stringify(this.list));
        });
    },
    addTask() {
      this.tasks.push({
        title: this.newTask,
        done: false
      });
      this.newTask = "";
      this.list[this.id].tasks = this.tasks;
      localStorage.setItem("list", JSON.stringify(this.list));
    }
  },
  props: ["id"],
  watch: {
    id: function(newval, oldval) {
      this.getTasks();
    }
  }
};
</script>

<style lang="scss">
.done {
  .q-item__label {
    text-decoration: line-through;
    color: #bbb;
  }
}
.no-tasks {
  opacity: 70%;
}
</style>
