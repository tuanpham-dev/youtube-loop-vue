<template>
  <header class="sticky-top navbar navbar-expand navbar-dark bg-dark">
    <div class="container">
      <a href="/" class="navbar-brand font-weight-bold text-uppercase">YouTube Loop</a>
      <div class="ml-3 d-flex">
        <button v-if="!isPlaying" @click="$emit('playVideo')" class="btn btn-secondary mr-1">Play</button>
        <button v-if="isPlaying" @click="$emit('stopVideo')" class="btn btn-secondary mr-1">Stop</button>
        <button v-if="showPrevNext" @click="$emit('prevVideo')" class="btn btn-secondary mr-1">Previous</button>
        <button v-if="showPrevNext" @click="$emit('nextVideo')" class="btn btn-secondary mr-1">Next</button>
      </div>
      <div class="ml-5 input-group">
        <input v-model="input" type="text" class="form-control" placeholder="Enter YouTube URL or Video ID" />
        <div class="input-group-append">
          <button class="btn btn-primary" @click="$emit('addVideo', getYoutubeVideoId())">Go Loop!</button>
        </div>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  props: {
    isPlaying: Boolean,
    showPrevNext: Boolean
  },
  data() {
    return {
      input: ''
    }
  },
  methods: {
    getYoutubeVideoId() {
      let url = this.input
      let videoId = ''
      url = url.replace(/(>|<)/gi,'').split(/(vi\/|v=|\/v\/|youtu\.be\/|\/embed\/)/)

      if (url[2] !== undefined) {
        videoId = url[2].split(/[^0-9a-z_-]/i)
        videoId = videoId[0]
      } else {
        videoId = url
      }

      return videoId
    }
  }
}
</script>