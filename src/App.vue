<template>
  <div class="app-container">
    <SettingsPanel @updateSettings="applySettings" />
    <RetroCard :author="author" :content="content" :explanation="explanation" :theme="theme" :font="font"
      :author-image="authorImage" :content-font-size="contentFontSize" :author-font-size="authorFontSize"
      :explanation-font-size="explanationFontSize" />
  </div>
</template>

<script>
import SettingsPanel from './components/SettingsPanel.vue';
import RetroCard from './components/RetroCard.vue';

export default {
  components: {
    SettingsPanel,
    RetroCard
  },
  data() {
    return {
      // 默认设置
      theme: 'dark',
      author: '——莫离',
      content: '不要太在意别人说的话，他们有嘴，但是不一定有脑子，有脑子，不一定有情商和人品。',
      explanation: 'Don\'t pay too much attention to what others say,They have mouths, but not necessarily brains,Having a brain doesn\'t necessarily mean having emotional intelligence and character.',
      font: '汇文明朝体',
      contentFontSize: 100,
      authorFontSize: 60,
      explanationFontSize: 60,
      authorImage: null // 新增的头像图片数据
    };
  },
  methods: {
    applySettings(settings) {
      // 接收从 SettingsPanel 组件传来的设置，并更新数据
      this.theme = settings.theme;
      this.author = settings.author;
      this.content = settings.content;
      this.explanation = settings.explanation;
      this.font = settings.font;
      this.contentFontSize = settings.contentFontSize;
      this.authorFontSize = settings.authorFontSize;
      this.explanationFontSize = settings.explanationFontSize;
      this.authorImage = settings.authorImage; // 确保接收 authorImage
    }
  }
};
</script>

<style>
.app-container {
  background-color: #1a1a1a;
  display: flex;
  flex-direction: row;
}
</style>