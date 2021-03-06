<template>
  <a-layout-sider
    :collapsed="collapsed"
    @collapse="onCollapse"
    :trigger="null"
    :collapsible="collapsible"
  >
    <div class="flex-row-center pa-7">
      <h3>Arutoria</h3>
    </div>
    <a-menu
      theme="dark"
      mode="inline"
      v-model:selectedKeys="selectedKeys"
      @select="onClickMenuItem"
    >
      <template v-for="menu in menus" :key="menu.key">
        <a-menu-item
          :key="menu.key"
          v-if="!menu.children || menu.children.length === 0"
        >
          <icon-font
            :extraCommonProps="{ class: 'icon-font' }"
            :type="menu.icon"
          />
          <span>{{ menu.title }}</span>
        </a-menu-item>
        <template v-else-if="menu.children && menu.children.length > 0">
          <sub-menu :menu="menu" :menuKey="menu.key"></sub-menu>
        </template>
      </template>
    </a-menu>
  </a-layout-sider>
</template>
<script lang="ts">
import { defineComponent, FunctionalComponent, ref, Ref } from "vue";
import { generateMenu } from "./generate";
import { useRoute } from "vue-router";
import SubMenu from "./SubMenu.vue";
import router from "@/router";
import IconFont from "./IconFont";

interface MenuItem {
  key: string;
  keyPath: string[];
  item?: Ref;
}

export default defineComponent({
  name: "Menu",
  props: {
    collapsed: Boolean,
    collapsible: Boolean
  },
  emits: ["update:collapsed"],
  components: {
    SubMenu,
    IconFont: IconFont as FunctionalComponent
  },
  setup(props, ctx) {
    const route = useRoute();
    const menus = generateMenu();
    const selectedKeys = ref<string[]>([route.path]);
    const onCollapse = (collapsed: boolean) => {
      ctx.emit("update:collapsed", collapsed);
    };

    const onClickMenuItem = (item: MenuItem) => {
      selectedKeys.value = [item.key];
      router.push(item.key);
    };

    return {
      selectedKeys,
      onClickMenuItem,
      onCollapse,
      menus
    };
  }
});
</script>
<style lang="less">
.icon-font {
  color: #fff;
}
</style>