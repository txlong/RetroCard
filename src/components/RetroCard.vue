<template>
  <div :class="['retro-card', theme]" :style="[cardStyle, contentStyle]">
    <p class="content">{{ content }}</p>
    <p class="author">{{ author }}</p>
    <p class="explanation">{{ explanation }}</p>
    <img v-if="authorImage" :src="authorImage" alt="Author's Avatar" class="author-avatar" />
  </div>
</template>

<script>
import wallTexture from '../images/wall_tiny.jpg';
import paperTexture from '../images/old-paper_tiny.png';
import neonTexture from '../images/texture-2.jpg';

export default {
  props: {
    theme: String,
    author: String,
    content: String,
    explanation: String,
    font: String,
    authorImage: String,
    contentFontSize: Number,
    authorFontSize: Number,
    explanationFontSize: Number
  },
  computed: {
    contentStyle() {
      return {
        '--content-font-size': `${this.contentFontSize || 100}px`,
        '--author-font-size': `${this.authorFontSize || 60}px`,
        '--explanation-font-size': `${this.explanationFontSize || 60}px`
      };
    },
    cardStyle() {
      let backgroundImage = '';
      if (this.theme === 'light') {
        backgroundImage = `url(${wallTexture})`;
      } else if (this.theme === 'dark') {
        backgroundImage = `url(${paperTexture})`;
      } else if (this.theme === 'neon') {
        backgroundImage = `url(${neonTexture})`;
      }
      return {
        backgroundImage: backgroundImage,
        backgroundSize: 'cover',
        backgroundPosition: 'center',
        backgroundRepeat: 'no-repeat',
        fontFamily: this.font,
        textAlign: 'left'
      };
    }
  }
};
</script>

<style scoped>
.retro-card {
  position: relative;
  margin: 20px 20px 20px 0;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  width: min(1080px, calc(100vw - 340px));
  aspect-ratio: 1080 / 1440;
  min-width: 0;
  overflow: hidden;
  padding: 48px 56px;
}

.retro-card::before {
  content: 'XXMY';
  position: absolute;
  right: 34px;
  bottom: 38px;
  z-index: 0;
  font-size: 72px;
  line-height: 0.9;
  font-family: 'Times New Roman', serif;
  font-weight: bold;
  letter-spacing: 0.16em;
  white-space: nowrap;
  transform-origin: right bottom;
  pointer-events: none;
  user-select: none;
}

.retro-card.light::before {
  color: rgba(52, 39, 26, 0.16);
  -webkit-text-stroke-color: rgba(255, 255, 255, 0.28);
}

.retro-card.dark::before {
  color: rgba(255, 244, 214, 0.13);
  -webkit-text-stroke-color: rgba(76, 55, 31, 0.2);
}

.retro-card.neon::before {
  color: rgba(255, 241, 170, 0.2);
  -webkit-text-stroke-color: rgba(255, 105, 180, 0.22);
}

p {
  margin: 0;
  box-sizing: border-box;
}

.content {
  position: relative;
  z-index: 1;
  max-width: 100%;
  font-size: var(--content-font-size);
  line-height: 1.25;
  white-space: pre-line;
  word-break: normal;
  overflow-wrap: break-word;
  text-wrap: balance;
}

.author {
  position: relative;
  z-index: 1;
  margin-top: 24px;
  font-size: var(--author-font-size);
  font-weight: bold;
}

.explanation {
  position: relative;
  z-index: 1;
  margin-top: auto;
  max-width: calc(100% - 220px);
  line-height: 1.35;
  font-size: var(--explanation-font-size);
  font-style: italic;
  white-space: pre-line;
  word-break: normal;
  overflow-wrap: break-word;
  text-wrap: balance;
  text-align: left;
}

.author-avatar {
  position: absolute;
  bottom: 30px;
  right: 50px;
  width: 180px;
  height: 180px;
  /* border-radius: 50%; */
  opacity: 0.75;
  object-fit: cover;
}

@media (max-width: 760px) {
  .retro-card {
    width: calc(100vw - 32px);
    margin: 16px;
    padding: 28px;
  }

  .content {
    font-size: min(42px, var(--content-font-size));
  }

  .author {
    font-size: min(24px, var(--author-font-size));
  }

  .explanation {
    max-width: 100%;
    font-size: min(16px, var(--explanation-font-size));
  }

  .author-avatar {
    width: 96px;
    height: 96px;
    right: 24px;
    bottom: 24px;
  }

  .retro-card::before {
    right: 20px;
    bottom: 24px;
    font-size: 34px;
    letter-spacing: 0.1em;
  }
}
</style>