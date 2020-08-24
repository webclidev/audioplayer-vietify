<template>
  <div id="app">
    <v-app app>
      <v-main>
        <v-container fluid class="grey darken-4 white--text container-player pa-0 pa-md-4">
          <v-row no-gutters>
            <v-col cols="7" sm="6" md="3" class="d-flex align-center">
              <div class="d-none d-sm-block">
                <v-img width="80" height="80" :src="imgsrc" />
              </div>
              <div class="px-5">
                <p class="subtitle-1 my-0">{{audio.title}}</p>
                <p class="subtitle-2 my-0">{{audio.subtitle}}</p>
              </div>
            </v-col>
            <v-col cols="5" sm="6" md="6" class="d-flex flex-column align-self-center">
              <div class="audio-controls align-self-center">
                <v-btn
                  color="white"
                  @click="previous"
                  icon
                  x-large
                  class="mx-3 d-none d-sm-inline-block"
                >
                  <v-icon>mdi-rewind-outline</v-icon>
                </v-btn>
                <v-btn color="white" @click="rewind" icon x-large class="mx-3">
                  <v-icon>mdi-rewind-10</v-icon>
                </v-btn>
                <v-btn color="white" @click="play" v-if="!isplaying" icon x-large class="mx-3">
                  <v-icon>mdi-play-circle-outline</v-icon>
                </v-btn>
                <v-btn color="white" @click="pause" v-if="isplaying" icon x-large class="mx-3">
                  <v-icon>mdi-pause-circle-outline</v-icon>
                </v-btn>
                <v-btn color="white" @click="ff" icon x-large class="mx-3 d-none d-md-inline-block">
                  <v-icon>mdi-fast-forward-10</v-icon>
                </v-btn>
                <v-btn
                  color="white"
                  @click="next"
                  icon
                  x-large
                  class="mx-3 d-none d-md-inline-block"
                >
                  <v-icon>mdi-fast-forward-outline</v-icon>
                </v-btn>
              </div>
              <div class="audio-timeline d-flex align-center align-self-center">
                <v-slider
                  class="d-none d-md-block"
                  color="#F50057"
                  thumb-color="#fff"
                  track-color="black"
                  height="10"
                  :value="(currentSeconds / durationSeconds) * 100"
                  :buffer-value="durationSeconds"
                  stream
                  @change="seek"
                ></v-slider>
                <span class="ml-5 d-none d-md-block">{{ durationSeconds | convertTimeHHMMSS }}</span>
              </div>
            </v-col>
            <v-col cols="0" md="3">
              <!-- <div>{{ currentSeconds | convertTimeHHMMSS }}</div> -->
            </v-col>
          </v-row>
          <v-progress-linear
            class="d-md-none"
            color="#F50057"
            bottom
            rounded
            background-color="black"
            height="10"
            :value="(currentSeconds / durationSeconds) * 100"
            :buffer-value="durationSeconds"
            stream
            @change="seek"
          ></v-progress-linear>
        </v-container>
      </v-main>
    </v-app>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      imgsrc: require('./assets/19.jpg'),
      audio: {},
      isplaying: false,
      index: 0,
      songs: [
        {
          title: 'Funny Song',
          subtitle: 'Ben sound',
          src: require('./assets/funnysong.mp3')
        },
        {
          title: 'Hip Jazz',
          subtitle: 'Ben Hur',
          src: require('./assets/hipjazz.mp3')
        },
        {
          title: 'Punky',
          subtitle: 'Punk hit',
          src: require('./assets/punky.mp3')
        }
      ],
      player: new Audio(),
      currentSeconds: 0,
      durationSeconds: 0,
    }
  },
  mounted() {
    this.setplayer();
    this.player.addEventListener('timeupdate', this.update);
    this.player.addEventListener('loadeddata', this.load);
    // this.player.addEventListener('pause', () => { this.isplaying = false; });
    // this.player.addEventListener('play', () => { this.isplaying = true; });
  },
  computed: {
    percentComplete() {
      return parseInt(this.currentSeconds / this.durationSeconds * 100);
    }
  },
  methods: {
    setplayer() {
      this.audio = this.songs[this.index]
      this.player['src'] = this.audio.src
    },
    play() {
      this.isplaying = true
      this.player.play()
    },
    pause() {
      this.isplaying = false
      this.player.pause()
    },
    next() {
      if (this.index < this.songs.length - 1) {
        this.index++
        this.setplayer()
        this.play()
      }
    },
    previous() {
      if (this.index > 0) {
        this.index--
        this.setplayer()
        this.play()
      }
    },
    update(e) {
      this.currentSeconds = parseInt(this.player.currentTime);
    },
    load() {
      if (this.player.readyState >= 2) {
        this.loaded = true;
        this.durationSeconds = parseInt(this.player.duration);
        return;
      }
      throw new Error('Failed to load sound file.');
    },
    seek(e) {
      if (!this.loaded) return;
      this.player.currentTime = (e * this.durationSeconds) / 100;
    },
    rewind(e) {
      if (!this.loaded) return;
      this.player.currentTime = this.currentSeconds - 10;
    },
    ff(e) {
      if (!this.loaded) return;
      this.player.currentTime = this.currentSeconds + 10;
    },
  },
  filters: {
    convertTimeHHMMSS(val) {
      let hhmmss = new Date(val * 1000).toISOString().substr(11, 8);
      return hhmmss.indexOf("00:") === 0 ? hhmmss.substr(3) : hhmmss;
    }
  },
}
</script>

<style lang="css" scoped >
.container-player {
  position: absolute;
  bottom: 0;
}
.audio-timeline {
  width: 360px;
}
::v-deep .v-input__slot {
  align-items: baseline;
}
</style>
