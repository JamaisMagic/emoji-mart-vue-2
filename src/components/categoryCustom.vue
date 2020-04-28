<template>

<div :class="{ 'emoji-mart-category': true, 'emoji-mart-no-results': !hasResults }" v-if="isVisible && (isSearch || hasResults)">
  <div class="emoji-mart-category-label">
    <span>{{ i18n.categories[id] }}<a
        v-if="isLockedGroup"
        href="javascript://"
        class="clear-btn"
        @click="$emit('unlock')"
      >{{ i18n.unlock }}</a></span>
  </div>

  <nimble-emoji
    v-for="emoji in renderEmojis"
    :key="emoji.id || emoji"
    :data="data"
    :emoji="emoji"
    :native="emojiProps.native"
    :skin="emojiProps.skin"
    :set="emojiProps.set"
    :size="emojiProps.sizeL"
    :sheet-size="emojiProps.sheetSize"
    :force-size="emojiProps.forceSize"
    :tooltip="emojiProps.tooltip"
    :background-image-fn="emojiProps.backgroundImageFn"
    :i18n="i18n"
    @click="emojiProps.onClick"
    @unlock="(data) => $emit('unlock', data)"
    @mouseenter="emojiProps.onEnter"
    @mouseleave="emojiProps.onLeave"
  />

  <div v-if="!hasResults">
    <nimble-emoji
      :data="data"
      :size="emojiProps.sizeL"
      emoji="sleuth_or_spy"
      :native="emojiProps.native"
      :skin="emojiProps.skin"
      :set="emojiProps.set"
      :sheet-size="emojiProps.sheetSize"
      :background-image-fn="emojiProps.backgroundImageFn"
    />
    <div class="emoji-mart-no-results-label">{{ i18n.notfound }}</div>
  </div>
</div>

</template>

<script>

import NimbleEmoji from './emoji/nimbleEmoji'

export default {
  data() {
    return {
      renderEmojis: [],
    }
  },
  props: {
    data: {
      type: Object,
      required: true
    },
    i18n: {
      type: Object,
      required: true
    },
    id: {
      type: String,
      required: true
    },
    name: {
      type: String,
      required: true
    },
    emojis: {
      type: Array
    },
    emojiProps: {
      type: Object,
      required: true
    }
  },
  computed: {
    isVisible() {
      return !!this.emojis
    },
    isSearch() {
      return this.name == 'Search'
    },
    hasResults() {
      return this.emojis.length > 0
    },
    isLockedGroup() {
      if (!this.emojis || this.emojis.length <= 0) {
        return false
      }
      return this.emojis.every((item) => item.locked)
    },
  },
  watch: {
    emojis: {
      handler(val) {
        if (!val) {
          return
        }
        if (val.length <= 50) {
          this.$nextTick(() => {
            this.renderEmojis = val
          })
          return
        }
        this.$nextTick(() => {
          this.renderEmojis = val.slice(0, 50)
          window.setTimeout(() => {
            this.renderEmojis = val
          }, 0)
        })
      },
      immediate: true,
    },
  },
  components: {
    NimbleEmoji
  }
}

</script>

<style scoped>

.emoji-mart-category {
  position: relative;
  padding: 4px 5px;
  border-bottom: 1px solid #ebebeb;
}

.emoji-mart-category-label {
  position: sticky;
  top: 0;
}

.emoji-mart-category .emoji-mart-emoji:before {
  z-index: 0;
  content: "";
  position: absolute;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background-color: #f4f4f4;
  border-radius: 100%;
  opacity: 0;
}

.emoji-mart-category .emoji-mart-emoji:hover:before {
  opacity: 1;
}

.emoji-mart-category-label {
  z-index: 2;
  position: relative;
  position: -webkit-sticky;
  position: sticky;
  top: 0;
}

.emoji-mart-category-label span {
  display: block;
  width: 100%;
  font-weight: 400;
  padding: 10px 6px 5px 6px;
  background-color: #fff;
  background-color: rgba(255, 255, 255, .95);
  color: #333333;
  font-size: 14px;
}

.emoji-mart-category-label .clear-btn {
  float: right;
  color: #333333;
  text-decoration: none;
}

.emoji-mart-no-results {
  font-size: 14px;
  text-align: center;
  padding-top: 70px;
  color: #858585;
}

.emoji-mart-no-results .emoji-mart-category-label {
  display: none;
}

.emoji-mart-no-results .emoji-mart-no-results-label {
  margin-top: .2em;
}

.emoji-mart-no-results .emoji-mart-emoji:hover:before {
  content: none;
}

</style>

<style>

.emoji-mart-category .emoji-mart-emoji span {
  z-index: 1;
  position: relative;
  text-align: center;
  cursor: default;
}

</style>
