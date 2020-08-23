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
      :width="300"
      :breakpoint="400"
    >
      <q-scroll-area
        style="height: calc(100% - 192px); margin-top: 192px; border-right: 1px solid #ddd"
      >
        <q-list padding>
          <q-item to="/" clickable v-ripple exact>
            <q-item-section avatar>
              <q-icon name="list" />
            </q-item-section>

            <q-item-section>
              日常
            </q-item-section>
          </q-item>
          <q-item to="/" clickable v-ripple exact>
            <q-item-section avatar>
              <q-icon name="list" />
            </q-item-section>

            <q-item-section>
              工作
            </q-item-section>
          </q-item>
        </q-list>
      </q-scroll-area>

      <div class="absolute-top bg-teal-4" style="height: 192px">
        <div
          class="bg-transparent  fit row  justify-center items-center q-px-md"
        >
          <q-avatar size="56px" class="col-8    " align="center">
            <img
              src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595910886276&di=92785bb39c382fa604588c8f999aac61&imgtype=0&src=http%3A%2F%2Fb-ssl.duitang.com%2Fuploads%2Fitem%2F20181%2F30%2F201813015155_Bfx8k.thumb.700_0.jpeg"
            />
          </q-avatar>
          <div class="text-weight-bold col-4 text-h5" align="left">
            霜序八
          </div>
        </div>
      </div>
    </q-drawer>

    <q-page-container>
      <keep-alive>
        <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script>
// import EssentialLink from "components/EssentialLink.vue";
import { date } from "quasar";

export default {
  name: "MainLayout",

  components: {
    // EssentialLink
  },

  data() {
    return {
      leftDrawerOpen: false
    };
  },
  computed: {
    dateNow: function() {
      let timeStamp = Date.now();
      return date.formatDate(timeStamp, "dddd D MMMM");
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
</style>
