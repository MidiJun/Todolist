<template>
  <q-page class="column page">
    <div class="contentPage">
      <div class="headPanel">
        <div class="date">
          <span class="text-h5">{{ list[id].name }}</span>
          <span class="text-subtitle1">{{ dateNow }}</span>
        </div>
        <div class="progress" v-show="tasks.length">
          {{ count + "/" + tasks.length }}
        </div>
      </div>
      <div v-if="tasks.length">
        <q-linear-progress :value="progress" color="white" class="q-mt-sm" />
      </div>
    </div>

    <q-btn
      color="white"
      text-color="secondary"
      class="addTask"
      icon="add"
      @click="prompt = true"
    />
    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">新建任务</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input
            dense
            v-model="newTask"
            autofocus
            @keyup.enter="addTask(), (prompt = false)"
          />
        </q-card-section>
        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="取消" v-close-popup />
          <q-btn flat label="完成" v-close-popup @click="addTask" />
        </q-card-actions>
      </q-card>
    </q-dialog>
    <!-- <div class="row bg-primary q-pa-sm">
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
    </div>-->

    <q-list class="taskList" v-if="tasks.length">
      <div v-for="(task, i) in tasks" :key="i">
        <q-item v-ripple clickable @click="taskStatus(task)" :class="done">
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
            <q-btn flat round class="taskDel" dense icon="delete" />
          </q-item-section>
        </q-item>
        <q-separator spaced inset="item" v-if="i < tasks.length - 1" />
      </div>
    </q-list>
    <div class="no-tasks absolute-center" v-if="!tasks.length">
      <q-icon name="check" size="100px" color="white" />
      <div class="text-h5 text-white text-center">No tasks</div>
    </div>
  </q-page>
</template>

<script>
import { date } from "quasar";
export default {
  name: "PageIndex",
  data() {
    return {
      newTask: "",
      list: [],
      prompt: false,
      tasks: [],
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
      progress: 0,
      count: 0
    };
  },
  created() {
    this.getTasks();
    this.setLineProgress();
  },
  computed: {
    dateNow: function() {
      let timeStamp = Date.now();
      return date.formatDate(timeStamp, "MMM DD").toUpperCase();
    }
  },
  methods: {
    getTasks() {
      this.list = JSON.parse(localStorage.getItem("list") || "[]");
      if (this.list[this.id].tasks) {
        this.tasks = this.list[this.id].tasks;
      } else this.tasks = [];
      console.log(this.tasks.length);
    },
    setLineProgress() {
      this.count = 0;
      for (let i = 0; i < this.tasks.length; i++) {
        if (this.tasks[i].done === true) {
          this.count++;
        }
      }
      this.progress = this.count / this.tasks.length;
    },
    taskStatus(task) {
      task.done = !task.done;
      this.list[this.id].tasks = this.tasks;
      this.setLineProgress();
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
          this.setLineProgress();
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
    },
    a() {
      console.log("a");
    },
    b() {
      console.log("b");
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
@import "../css/mixin.scss";
.page {
  background: linear-gradient(#f1873a, #d62e33);
}
.contentPage {
  margin: 20px;
  margin-bottom: 45px;
}
.headPanel {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  color: rgba(255, 255, 255, 0.5);
  .date {
    span:first-child {
      margin-right: 10px;
      font: bold 40px "Fjalla One", sans-serif;
      color: #fff;
    }
    span:last-child {
      font: 20px "Fjalla One";
    }
  }
}
.no-tasks {
  opacity: 80%;
}
.addTask {
  @include squareBtn;
}
.taskList {
  color: #fff;
  font-size: 18px;
}
.q-checkbox__inner {
  width: 1.2em;
  height: 1.2em;
}
.q-checkbox__bg {
  border-radius: 50%;
  color: rgba(255, 255, 255, 0.6);
}
.taskDel {
  color: rgba(255, 255, 255, 0.6);
}
.q-checkbox__inner--truthy {
  .q-checkbox__bg {
    border-radius: 50%;
    color: rgba(255, 255, 255, 1);
    svg {
      color: #f1591c;
    }
  }
}

.q-separator {
  background-color: rgba(255, 255, 255, 0.4);
}
.q-separator--horizontal {
  width: auto;
  margin-right: 20px;
}
</style>
