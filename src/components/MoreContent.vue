<script setup>
import { ref, onMounted } from 'vue';

// 定义一个响应式变量来存储一言文本
const hitokoto = ref('正在加载一言...');

// 组件挂载后执行的逻辑
// 这是需要修改的 <script setup> 部分
onMounted(() => {
  // 在链接后加上 ?c=d 来指定“文学”分类
  fetch('https://v1.hitokoto.cn/?c=d') // <--- 修改这里的链接
    .then(res => res.json())
    .then(data => {
      hitokoto.value = data.hitokoto;
    })
    .catch(error => {
      console.error("获取一言失败:", error);
      hitokoto.value = '今天也要开心呀。';
    });
});
</script>

<template>
  <div class="more-content">
    <p class="hitokoto-text">{{ hitokoto }}</p>
  </div>
</template>

<style lang="scss" scoped>
/*
  这个 CSS 规则就是让文本居中的关键。
  它使用 Flexbox 布局，并通过 align-items 和 justify-content
  让内部的元素在垂直和水平方向上都居中。
*/
.more-content {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 20px;
  width: 100%;
  height: 100%;
}

/*
  为“一言”文本本身添加一些样式，让它更好看。
*/
.hitokoto-text {
  color: rgba(255, 255, 255, 0.9); /* 使用柔和的白色 */
  font-size: 15px;
  padding: 10px 20px;
  background-color: rgba(0, 0, 0, 0.1); /* 加一点淡淡的背景，增加层次感 */
  border-radius: 8px; /* 圆角 */
  text-align: center;
  max-width: 90%; /* 防止文本过长时撑满整个宽度 */
  line-height: 1.7; /* 调整行高，让多行文本更舒适 */
}
</style>