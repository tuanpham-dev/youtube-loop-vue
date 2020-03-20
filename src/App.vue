<template>
  <div id="app">
    <Header
      :isPlaying="playingVideo !== -1"
      :showPrevNext="this.videos.length > 1"
      @addVideo="addVideo"
      @playVideo="playFirstVideo"
      @stopVideo="stopPlaying"
      @prevVideo="playPrevVideo"
      @nextVideo="playNextVideo"
    />

    <div class="container mt-5">
      <ol class="video-list">
        <li v-for="(video, i) in videos" :key="video.id" class="video-item d-flex flex-column justify-content-center align-items-center mb-5">
          <span :v-html="JSON.stringify(videos)"></span>
          <YouTube
            :isPlaying="video.id === playingVideo"
            :videoId="video.video"
            @play="handleVideoPlay(i)"
            @end="handleVideoEnd(i)"
          />
          <div class="text-center mt-3">
            <button v-if="i > 0" @click="moveVideoUp(i)" class="btn btn-secondary mr-1">Move Up</button>
            <button v-if="i < videos.length - 1" @click="moveVideoDown(i)" class="btn btn-secondary mr-1">Move Down</button>
            <button @click="removeVideo(i)" class="btn btn-danger">Remove Video</button>
          </div>
        </li>
      </ol>
    </div>
  </div>
</template>

<script>
import localStorage from 'local-storage'
import Header from './components/Header.vue'
import YouTube from './components/YouTube.vue'

export default {
  name: 'App',
  components: {
    Header,
    YouTube
  },
  data() {
    return {
      nextId: 0,
      videos: [],
      playingVideo: -1
    }
  },
  mounted() {
    const savedVideos = localStorage.get('videos')
    const videos = []

    if (savedVideos) {
      savedVideos.forEach(video => {
        videos.push({
          id: this.getNextId(),
          video: video.video
        })
      })
    }

    this.videos = videos
  },
  methods: {
    getNextId() {
      return this.nextId++
    },

    addVideo(youtubeVideoId) {
      const nextId = this.getNextId()
      const videos = [...this.videos, {
        id: nextId,
        video: youtubeVideoId
      }]

      this.videos = videos
      localStorage.set('videos', videos)
    },

    removeVideo(i) {
      const videos = [...this.videos]
      videos.splice(i, 1)

      this.videos = videos
    },

    moveVideoUp(i) {
      const videos = [...this.videos]
      const currentVideo = videos[i]

      videos.splice(i, 1)
      videos.splice(i - 1, 0, currentVideo)

      this.videos = videos
      localStorage.set('videos', videos)
    },

    moveVideoDown(i) {
      const videos = [...this.videos]
      const currentVideo = videos[i]

      videos.splice(i, 1)
      videos.splice(i + 1, 0, currentVideo)

      this.videos = videos
      localStorage.set('videos', videos)
    },

    stopPlaying() {
      this.playingVideo = -1
    },

    playFirstVideo() {
      if (this.videos.length > 0) {
        this.playingVideo = this.videos[0].id
      }
    },

    findPlayingVideoIndex() {
      for (let i = 0; i < this.videos.length; i++) {
        if (this.videos[i].id === this.playingVideo) {
          return i
        }
      }
    },

    playNextVideo() {
      let playingVideo = -1
      const i = this.findPlayingVideoIndex()

      if (i === this.videos.length - 1) {
        playingVideo = this.videos[0].id
      } else {
        playingVideo = this.videos[i + 1].id
      }

      this.playingVideo = playingVideo
    },

    playPrevVideo() {
      let playingVideo = -1
      const i = this.findPlayingVideoIndex()

      if (i === 0) {
        playingVideo = this.videos[this.videos.length - 1].id
      } else {
        playingVideo = this.videos[i - 1].id
      }

      this.playingVideo = playingVideo
    },

    handleVideoPlay(i) {
      this.playingVideo = this.videos[i].id
    },

    handleVideoEnd() {
      this.playNextVideo()
    }
  }
}
</script>
