<template>
  <q-layout view="lHh Lpr lFf">
    <q-header>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />
      </q-toolbar>

      <div class="q-px-lg q-pt-xl q-mb-md">
        <div class="text-h3">Todo</div>
        <div class="text-subtitle1">{{ dateNow }}</div>
      </div>
      <q-img src="../statics/bloom.jpg" class="header-image absolute-top" />
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      :width="260"
      :breakpoint="400"
    >
      <div class="absolute-top userPanel" style="height: 210px">
        <img src="../statics/pic.jpeg" />
        <div class="userInfo" align="left">
          <div>Guo Jun</div>
          <div class="text-grey">junguo0908@gmail.com</div>
        </div>
      </div>

      <q-scroll-area style="height: calc(100% - 210px); margin-top: 210px;">
        <q-list padding>
          <q-item
            v-for="(item, index) in list"
            :key="index"
            clickable
            :active="link === '/' + index"
            @click="listTo(index)"
            v-ripple
            exact
          >
            <q-item-section avatar>
              <q-icon name="list" />
            </q-item-section>
            <q-item-section>
              {{ item.name }}
            </q-item-section>
            <!-- <q-item-section avatar @click.stop="listChange">
              <q-icon name="more_vert" />
            </q-item-section> -->

            <q-item-section side @click.stop="deleteList(index)">
              <q-btn flat round dense icon="delete" />
            </q-item-section>
          </q-item>
        </q-list>
        <q-btn
          round
          color="primary"
          class="addlist"
          icon="add"
          @click="prompt = true"
        />
      </q-scroll-area>
    </q-drawer>

    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">新建清单</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input
            dense
            v-model="newListName"
            autofocus
            @keyup.enter="prompt = false"
          />
        </q-card-section>
        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="取消" v-close-popup />
          <q-btn flat label="完成" v-close-popup @click="addList" />
        </q-card-actions>
      </q-card>
    </q-dialog>
    <q-page-container>
      <keep-alive>
        <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script>
import { date } from "quasar";

export default {
  name: "MainLayout",

  components: {},
  created() {
    this.getList();
  },
  data() {
    return {
      leftDrawerOpen: false,
      alert: false,
      confirm: false,
      prompt: false,
      newListName: [], //新清单名
      list: [],
      link: ""
    };
  },
  computed: {
    dateNow: function() {
      let timeStamp = Date.now();
      return date.formatDate(timeStamp, "dddd D MMMM");
    }
  },
  methods: {
    getList() {
      this.list = JSON.parse(localStorage.getItem("list") || "[]");
    },
    addList() {
      this.list.push({ name: this.newListName });
      this.newListName = "";
      localStorage.setItem("list", JSON.stringify(this.list));
    },
    listTo(i) {
      this.link = "/" + i;
      this.$router.push("/" + i);
    },
    deleteList(index) {
      this.$q
        .dialog({
          title: "提示",
          message: "确定要删除此清单吗?",
          cancel: true,
          persistent: true
        })
        .onOk(() => {
          this.list = JSON.parse(localStorage.getItem("list") || "[]");
          this.list.splice(index, 1);
          this.$q.notify({
            message: "此清单已删除",
            icon: "done"
          });
          localStorage.setItem("list", JSON.stringify(this.list));
        });
    }
  }
};
</script>

<style lang="scss">
.header-image {
  height: 100%;
  z-index: -1;
  opacity: 90%;
  // filter: grayscale(20%);
}
.q-drawer-container {
  .userPanel {
    margin-left: 30px;
    img {
      width: 100%;
      float: right;
    }
    .userInfo {
      margin-top: 140px;
      div:first-child {
        font: bold 28px Britannic;
      }
    }
  }
  .addlist {
    position: fixed;
    right: 30px;
    bottom: 30px;
  }
}
</style>
