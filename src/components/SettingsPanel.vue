<template>
  <div class="settings-panel">
    <h2>一言卡片</h2>
    <!-- Theme Selection -->
    <label for="theme-select">主题:</label>
    <select id="theme" v-model="theme">
      <option value="light">Light</option>
      <option value="dark">Dark</option>
      <option value="neon">Neon</option>
    </select>

    <!-- Content Input -->
    <label for="content">内容:</label>
    <textarea v-model="content" id="content" rows="5" placeholder="Enter content"></textarea>

    <!-- Author Input -->
    <label for="author">作者:</label>
    <input type="text" id="author" v-model="author" placeholder="Enter author name" />

    <!-- Explanation Input -->
    <label for="explanation">解释:</label>
    <textarea v-model="explanation" id="explanation" rows="3" placeholder="Enter explanation"></textarea>

    <div class="font-size-settings">
      <label for="content-font-size">内容字号: <output>{{ contentFontSize }}px</output></label>
      <input id="content-font-size" v-model.number="contentFontSize" type="range" min="80" max="200" step="1" />

      <label for="author-font-size">作者字号: <output>{{ authorFontSize }}px</output></label>
      <input id="author-font-size" v-model.number="authorFontSize" type="range" min="40" max="100" step="1" />

      <label for="explanation-font-size">解释字号: <output>{{ explanationFontSize }}px</output></label>
      <input id="explanation-font-size" v-model.number="explanationFontSize" type="range" min="30" max="80" step="1" />
    </div>

    <!-- Font Selection -->
    <label for="font">字体:</label>
    <select id="font" v-model="font">
      <option v-for="font in fonts" :key="font" :value="font">{{ font }}</option>
    </select>

    <!-- Author Image Upload -->
    <label for="author-image">作者头像:</label>
    <input type="file" id="avatar" @change="onImageChange" accept="image/*" />

    <div class="btn-view">
      <button @click="applySettings">应用</button>
      <button @click="downloadPng">下载</button>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas';

export default {
  data() {
    return {
      // 本地状态，初始化默认值
      theme: 'dark',
      author: '——莫离',
      content: '不要太在意别人说的话，他们有嘴，但是不一定有脑子，有脑子，不一定有情商和人品。',
      explanation: 'Don\'t pay too much attention to what others say,They have mouths, but not necessarily brains,Having a brain doesn\'t necessarily mean having emotional intelligence and character.',
      font: '汇文明朝体', // 默认字体
      contentFontSize: 100,
      authorFontSize: 60,
      explanationFontSize: 60,
      fonts: [], // 新增的字体数组
      authorImage: null // 新增的头像图片数据
    };
  },
  created() {
    this.loadFonts();
  },
  watch: {
    theme: 'emitSettings',
    author: 'emitSettings',
    content: 'emitSettings',
    explanation: 'emitSettings',
    font: 'emitSettings',
    authorImage: 'emitSettings',
    contentFontSize: 'emitSettings',
    authorFontSize: 'emitSettings',
    explanationFontSize: 'emitSettings'
  },
  methods: {
    emitSettings() {
      // 将当前设置传递给父组件
      this.$emit('updateSettings', {
        theme: this.theme,
        author: this.author,
        content: this.content,
        explanation: this.explanation,
        font: this.font,
        contentFontSize: this.contentFontSize,
        authorFontSize: this.authorFontSize,
        explanationFontSize: this.explanationFontSize,
        authorImage: this.authorImage
      });
    },
    applySettings() {
      this.emitSettings();
    },
    onImageChange(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          this.authorImage = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
    async downloadPng() {
      const cardElement = document.querySelector('.retro-card');
      if (!cardElement) return;

      try {
        // 等待字体加载完成
        await document.fonts.ready;

        // 确保所有图片加载完成
        const images = cardElement.querySelectorAll('img');
        await Promise.all(Array.from(images).map(img => {
          if (!img.complete) {
            return new Promise(resolve => {
              img.onload = resolve;
              img.onerror = resolve; // 处理加载错误
            });
          }
        }));

        // 给浏览器一点时间来完成渲染
        await new Promise(resolve => setTimeout(resolve, 100));

        const canvas = await html2canvas(cardElement, {
          backgroundColor: null,
          useCORS: true,
          scale: 2, // 提高输出质量
          logging: false,
          allowTaint: true,
          onclone: (clonedDoc) => {
            const clonedElement = clonedDoc.querySelector('.retro-card');
            if (clonedElement) {
              const styles = window.getComputedStyle(cardElement);
              Array.from(styles).forEach(key => {
                clonedElement.style.setProperty(key, styles.getPropertyValue(key));
              });
            }
          }
        });

        // 创建下载链接
        const link = document.createElement('a');
        link.href = canvas.toDataURL('image/png', 1.0);
        link.download = 'retro-card.png';
        link.click();
      } catch (error) {
        console.error('Error generating image:', error);
      }
    },
    loadFonts() {
      // 从woff2目录加载字体
      const fontFiles = [
        { name: '汇文明朝体', file: 'ming.fb17d0be.otf' },
        { name: '钉钉进步体', file: 'JinBuTi.780e62dd.ttf' },
        { name: '文楷体', file: 'LXGWWenKaiTC-Regular.c16ab11b.ttf' },
        { name: '白路棒棒手写体', file: '白路棒棒手写体.woff2' },
        { name: '方舟像素字体-12px-等宽', file: '方舟像素字体-12px-等宽.woff2' },
        { name: '胡晓波骚包体', file: '胡晓波骚包体.woff2' },
        { name: '千图小兔体', file: '千图小兔体.woff2' },
        { name: '香萃打字机体W15', file: '香萃打字机体W15.woff2' },
        { name: '曉聲通秋茄', file: '曉聲通秋茄.woff2' },
        { name: '演示秋鸿楷', file: '演示秋鸿楷.woff2' },
      ];
      this.fonts = fontFiles.map(font => font.name);

      // 动态加载字体
      fontFiles.forEach(font => {
        if (font.file) {
          const fontUrl = new URL(`../woff2/${font.file}`, import.meta.url).href;
          const newFontFace = new FontFace(font.name, `url(${fontUrl})`);
          newFontFace.load().then(loadedFont => {
            document.fonts.add(loadedFont);
          });
        }
      });
    }
  }
};
</script>

<style scoped>
.settings-panel {
  margin: 20px;
  /* 修改背景颜色 */
  color: #e0e0e0;
  /* 修改文字颜色 */
  width: 280px;
  /* 修改宽度 */
  padding: 20px;
  /* 修改内边距 */
  display: flex;
  flex-direction: column;
  border: 2px solid #333;
  /* 添加边框 */
  border-radius: 10px;
  /* 添加圆角 */
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  /* 添加阴影 */
}

.settings-panel h2 {
  color: #ffcc00;
  /* 修改标题颜色 */
  font-family: 'Press Start 2P', cursive;
  /* 修改字体 */
  text-align: center;
  /* 居中对齐 */
}

.settings-panel label {
  font-family: 'Press Start 2P', cursive;
  /* 修改字体 */
  margin-bottom: 8px;
  /* 修改下边距 */
}

.settings-panel select,
.settings-panel input,
.settings-panel textarea {
  margin-bottom: 15px;
  /* 修改下边距 */
  background-color: #333;
  /* 修改背景颜色 */
  border: 2px solid #555;
  /* 修改边框 */
  padding: 10px;
  /* 修改内边距 */
  color: #e0e0e0;
  /* 修改文字颜色 */
  border-radius: 5px;
  /* 添加圆角 */
}

.font-size-settings {
  margin-bottom: 10px;
  padding: 12px 0 2px;
  border-top: 1px solid #444;
}

.font-size-settings label {
  display: flex;
  justify-content: space-between;
  margin: 10px 0 5px;
}

.font-size-settings output {
  color: #ffcc00;
  font-variant-numeric: tabular-nums;
}

.settings-panel .font-size-settings input[type='range'] {
  width: 100%;
  margin: 0 0 4px;
  padding: 0;
  accent-color: #ffcc00;
}

.btn-view {
  display: flex;
  justify-content: space-between;
}

.settings-panel button {
  background-color: #ffcc00;
  /* 修改背景颜色 */
  color: #1a1a1a;
  /* 修改文字颜色 */
  cursor: pointer;
  padding: 8px 10px;
  /* 修改内边距 */
  border: none;
  /* 去掉边框 */
  border-radius: 5px;
  /* 添加圆角 */
  font-family: 'Press Start 2P', cursive;
  /* 修改字体 */
  transition: background-color 0.3s;
  /* 添加过渡效果 */
  min-width: 80px;
}

.settings-panel button:hover {
  background-color: #e6b800;
  /* 添加悬停效果 */
}
</style>