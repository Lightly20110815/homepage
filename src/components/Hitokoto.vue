<template>
  <div
    class="hitokoto cards"
    v-show="!store.musicOpenState"
    @mouseenter="openMusicShow = true"
    @mouseleave="openMusicShow = false"
    @click.stop
  >
    <Transition name="el-fade-in-linear">
      <div
        class="open-music"
        v-show="openMusicShow && store.musicIsOk"
        @click="store.musicOpenState = true"
      >
        <music-menu theme="filled" size="18" fill="#efefef" />
        <span>播放音乐</span>
      </div>
    </Transition>
    <Transition name="el-fade-in-linear" mode="out-in">
      <div :key="hitokotoData.text" class="content" @click="updateHitokoto">
        <span class="text">{{ hitokotoData.text }}</span>
        <span class="from">-「&nbsp;{{ hitokotoData.from }}&nbsp;」</span>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { MusicMenu, Error as ErrorIcon } from "@icon-park/vue-next";
import { getHitokoto } from "@/api";
import { mainStore } from "@/store";
import debounce from "@/utils/debounce.js";
import { ref, reactive, onMounted, h } from 'vue';

const store = mainStore();

// 开启音乐面板按钮显隐
const openMusicShow = ref(false);

// 一言数据
const hitokotoData = reactive({
  // 初始值也应该与失败时的文本保持一致，或者是一个通用的加载提示
  text: "With you, through all.", // 初始文本设置为您的自定义句子
  from: "Sy Yann",
});

// 获取一言数据
const getHitokotoData = async () => {
  try {
    const result = await getHitokoto();
    if (result && result.hitokoto) {
      hitokotoData.text = result.hitokoto;
      hitokotoData.from = result.from || "Sy Yann"; 
    } else {
      console.warn("一言API返回数据格式不正确，使用默认文本。");
      hitokotoData.text = "With you, through all."; // API返回数据格式不正确时也使用您的句子
      hitokotoData.from = "Sy Yann";
    }
  } catch (error) {
    console.error("一言获取失败：", error);
    if (typeof ElMessage !== 'undefined') {
      ElMessage({
        message: "一言加载失败，请稍后再试",
        icon: h(ErrorIcon, {
          theme: "filled",
          fill: "#efefef",
        }),
      });
    } else {
      console.warn("ElMessage未定义，无法显示错误提示。");
    }
    
    // 一言获取失败时使用您的自定义句子
    hitokotoData.text = "With you, through all."; 
    hitokotoData.from = "Sy Yann";
  }
};

// 更新一言数据
const updateHitokoto = () => {
  // 防抖
  debounce(() => {
    getHitokotoData();
  }, 500);
};

onMounted(() => {
  getHitokotoData();
});
</script>

<style lang="scss" scoped>
.hitokoto {
  width: 100%;
  height: 100%;
  padding: 20px;
  animation: fade 0.5s;
  .open-music {
    width: 100%;
    position: absolute;
    top: 0;
    left: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #00000026;
    padding: 4px 0;
    border-radius: 8px 8px 0 0;
    .i-icon {
      width: 18px;
      height: 18px;
      display: block;
      margin-right: 8px;
    }
    span {
      font-size: 0.95rem;
    }
  }
  .content {
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    .text {
      font-size: 1.1rem;
      word-break: break-all;
      text-overflow: ellipsis;
      overflow: hidden;
      display: -webkit-box;
      -webkit-line-clamp: 3;
      -webkit-box-orient: vertical;
    }
    .from {
      margin-top: 10px;
      font-weight: bold;
      align-self: flex-end;
      font-size: 1.1rem;
    }
  }
}
</style>