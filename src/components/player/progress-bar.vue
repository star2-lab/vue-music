<template>
  <div
    class="progress-bar"
    @click="onClick"
  >
    <div class="bar-inner">
      <div
        class="progress"
        ref="progress"
        :style="progressStyle"
      ></div>
      <div
        class="progress-btn-wrapper"
        :style="btnStyle"
        @touchstart.prevent="onTouchStart"
        @touchmove.prevent="onTouchMove"
        @touchend.prevent="onTouchEnd"
      >
        <div class="progress-btn"></div>
      </div>
    </div>
  </div>
</template>

<script>
  const progressBtnWidth = 16

  export default {
    name: 'progress-bar',
    props: {
      progress: {
        type: Number,
        default: 0
      }
    },
    emits: ['progress-changing', 'progress-changed'],
    data() {
      return {
        offset: 0
      }
    },
    watch: {
      progress(newProgress) {
        this.setOffset(newProgress)
      }
    },
    computed: {
      progressStyle() {
        return `width: ${this.offset}px`
      },
      btnStyle() {
        return `transform: translate3d(${this.offset}px, 0, 0)`
      }
    },
    created() {
      this.touch = {}
    },
    methods: {
      onTouchStart(e) {
        this.touch.x1 = e.changedTouches[0].pageX
        this.touch.beginWidth = this.$refs.progress.clientWidth
      },
      onTouchMove(e) {
        const delta = e.changedTouches[0].pageX - this.touch.x1
        const tempWidth = this.touch.beginWidth + delta
        const barWidth = this.$el.clientWidth - progressBtnWidth
        const progress = Math.min(1, Math.max(0, tempWidth / barWidth))
        this.offset = barWidth * progress
        this.$emit('progress-changing', progress)
      },
      onTouchEnd() {
        const barWidth = this.$el.clientWidth - progressBtnWidth
        const progress = this.$refs.progress.clientWidth / barWidth
        this.$emit('progress-changed', progress)
      },
      onClick(e) {
        const rect = this.$el.getBoundingClientRect()
        const offetWidth = e.pageX - rect.left
        const barWidth = this.$el.clientWidth - progressBtnWidth
        const progress = offetWidth / barWidth
        this.$emit('progress-changed', progress)
      },
      setOffset(progress) {
        const barWidth = this.$el.clientWidth - progressBtnWidth
        this.offset = barWidth * progress
      }
    }
  }
</script>

<style lang="scss" scoped>
  .progress-bar {
    height: 30px;
    .bar-inner {
      position: relative;
      top: 13px;
      height: 4px;
      background: rgba(0, 0, 0, 0.3);
      .progress {
        position: absolute;
        height: 100%;
        background: $color-theme;
      }
      .progress-btn-wrapper {
        position: absolute;
        left: -8px;
        top: -13px;
        width: 30px;
        height: 30px;
        .progress-btn {
          position: relative;
          top: 7px;
          left: 7px;
          box-sizing: border-box;
          width: 16px;
          height: 16px;
          border: 3px solid $color-text;
          border-radius: 50%;
          background: $color-theme;
        }
      }
    }
  }
</style>
