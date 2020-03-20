<template>
  <div></div>
</template>

<script>
import loadYouTubeAPI from '../lib/loadYouTubeAPI'

export default {
  props: {
    videoId: String,
    isPlaying: Boolean
  },
  methods: {
    onReady() {
      if (this.isPlaying) {
        this.playerInstance.playVideo()
      }

      this.$emit('ready')
    }
  },
  watch: {
    isPlaying(newVal, oldVal) {
      if ( typeof this.playerInstance === 'object' && typeof this.playerInstance.playVideo === 'function' && newVal !== oldVal) {
        if (newVal) {
          this.playerInstance.seekTo(0)
          this.playerInstance.playVideo()
        } else {
          this.playerInstance.pauseVideo()
        }
      }
    }
  },
  mounted() {
    this.player = loadYouTubeAPI().then(YT => {
      this.playerInstance = new YT.Player(this.$el, {
        videoId: this.videoId,
        events: {
          onReady: this.onReady,
          onStateChange: (event) => {
            if (event.data === YT.PlayerState.PLAYING) {
              this.$emit('play')
            } else if (event.data === YT.PlayerState.PAUSED) {
              this.$emit('pause')
            } else if (event.data === YT.PlayerState.ENDED) {
              this.$emit('end')
            }
          }
        }
      })
    })
  }
}
</script>