<template>
  <div class="singer-detail">
    <MusicList :songs="songs" :title="title" :pic="pic" :loading="loading"/>
  </div>
</template>

<script>
import { getSingerDetail } from '@/service/singer'
import { processSongs } from '@/service/song'
import MusicList from '@/components/music-list/music-list'
import storage from 'good-storage'
import { SINGER_KEY } from '@/assets/js/constant.js'

export default {
  name: 'singer-detail',
  data() {
    return {
      songs: [],
      loading: true
    }
  },
  props: {
    singer: Object
  },
  async created() {
    if (!this.computedSinger) {
      const path = this.$route.matched[0].path
      this.$router.push({
        path
      })
      return
    }
    const res = await getSingerDetail(this.computedSinger)
    const songs = await processSongs(res.songs)
    this.songs = songs
    this.loading = false
  },
  components: {
    MusicList
  },
  computed: {
    pic() {
      const singer = this.computedSinger
      return singer && singer.pic
    },
    title() {
      const singer = this.computedSinger
      return singer && singer.name
    },
    computedSinger() {
      let ret = null
      const singer = this.singer
      if (singer) {
        ret = singer
      } else {
        const cacheSinger = storage.session.get(SINGER_KEY)
        if (cacheSinger && cacheSinger?.mid === this.$route.params.mid) {
          ret = cacheSinger
        }
      }
      return ret
    }
  }
}
</script>

<style lang="scss" scoped>
  .singer-detail {
    position: fixed;
    z-index: 10;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background: $color-background;
  }
</style>
