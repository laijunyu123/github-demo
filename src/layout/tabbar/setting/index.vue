<template>
  <el-button
    size="small"
    icon="Refresh"
    circle
    @click="updateRefsh"
  ></el-button>
  <el-button
    size="small"
    icon="FullScreen"
    circle
    @click="fullScreen"
  ></el-button>

  <!-- 
      popover:弹出框组件
      placement:出现的位置
  -->
  <el-popover placement="bottom" title="主题设置" :width="300" trigger="click">
    <!-- 表单组件 -->
    <el-form>
      <el-form-item label="主题设置" size="small">
        <el-color-picker
          v-model="color"
          show-alpha
          :predefine="predefineColors"
          @change="setColor"
        />
      </el-form-item>
      <el-form-item label="暗黑模式">
        <el-switch
          v-model="dark"
          size="small"
          :active-icon="MoonNight"
          :inactive-icon="Sunny"
          @change="changeDark"
        />
      </el-form-item>
    </el-form>
    <template #reference>
      <el-button size="small" icon="setting" circle></el-button>
    </template>
  </el-popover>
  <img
    :src="userStore.avatar"
    style="width: 24px; height: 24px; margin: 0 10px; border-radius: 50%"
  />
  <!-- 下拉菜单 -->
  <el-dropdown>
    <span class="el-dropdown-link">
      {{ userStore.username }}
      <el-icon class="el-icon--right">
        <arrow-down />
      </el-icon>
    </span>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item @click="logout">退出登录</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>

<script lang="ts">
export default {
  name: 'Setting',
};
</script>

<script setup lang="ts">
// 获取用户相关的小仓库
import { MoonNight, Sunny } from '@element-plus/icons-vue';
import { ref } from 'vue';
import useUserStore from '@/store/modules/user';
import { useRouter, useRoute } from 'vue-router';
// 获取骨架的小仓库
import useLayOutSettingStore from '@/store/modules/setting';
let layoutSettingStore = useLayOutSettingStore();
let userStore = useUserStore();
// 获取路由器对象
let $router = useRouter();
// 获取路由对象
let $route = useRoute();
// 收集开关的数据
let dark = ref<boolean>(false);

// 刷新按钮点击的回调
const updateRefsh = () => {
  layoutSettingStore.refsh = !layoutSettingStore.refsh;
};

// 全屏按钮点击的回调
const fullScreen = () => {
  // DOM 对象的一个属性，可以用来判断是否是全屏(全屏：true,不是全屏：null)
  let full = document.fullscreenElement;
  if (!full) {
    // 利用文档的 requestFullscreen 去实现全屏模式
    document.documentElement.requestFullscreen();
  } else {
    // 退出全屏模式
    document.exitFullscreen();
  }
};
// 退出登录按钮回调
const logout = async () => {
  // 第一件事情：需要向服务器发请求[退出登录接口] ********
  // 第二件事情：仓库当中关于用户相关的数据清空[token|avatar|username]
  // 第三件事情：跳转到登录页面
  await userStore.userLogout();
  // 跳转到登录页面
  $router.push({
    path: '/login',
    query: { redirect: $route.path },
  });
};

// 颜色组件需要的数据
const color = ref('rgba(255, 69, 0, 0.68)');
const predefineColors = ref([
  '#ff4500',
  '#ff8c00',
  '#ffd700',
  '#90ee90',
  '#00ced1',
  '#1e90ff',
  '#c71585',
  'rgba(255, 69, 0, 0.68)',
  'rgb(255, 120, 0)',
  'hsv(51, 100, 98)',
  'hsva(120, 40, 94, 0.5)',
  'hsl(181, 100%, 37%)',
  'hsla(209, 100%, 56%, 0.73)',
  '#c7158577',
]);
// switch 开关的 change 事件进行暗黑模式的切换
const changeDark = () => {
  // 获取 HTML 根节点
  let html = document.documentElement;
  dark.value ? (html.className = 'dark') : (html.className = '');
};
// 主题颜色的设置
const setColor = () => {
  // 通知 js 修改根节点的样式对象的属性与属性值
  const html = document.documentElement;
  html.style.setProperty('--el-color-primary', color.value);
};
</script>

<style scoped lang="scss"></style>
