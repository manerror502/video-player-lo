<template>
  <div>
    <p :class="{show: !loading}">
      Загрузка
    </p>

    <div
      ref="videoContainer"
      class="videoplayer"
      :class="{play: stateVideo == 'play', show: loading}"
    >
      <!-- Из папки public, webpack тварь -->
      <video
        ref="videoPlayer"
        :src="'video.'+quality+'.mp4'"
        class="videoplayer__video"
      >
        Нету
      </video>

      <div
        ref="progress"
        class="videobar__container"
        @mousemove="videoPlayertPreview"
        @click="videoRewind"
      >
        <div
          :style="{width: progress + '%'}"
          class="videobar__progress"
        />
      </div>

      <div
        v-if="out"
        class="videoplayer__title"
      >
        <h2>Title</h2>
      </div>

      <p class="videoplayer__time">
        {{ previewTime }}
      </p>
      <video
        ref="videoPlayertPreview"
        muted
        class="videoplayer__video videoplayer__video--preview"
      >
        <source src="video.320.mp4">
      </video>

      <div class="videoplayer__btn">
        <div class="videoplayer__btn-left">
          <button
            v-if="stateVideo == 'stopped' || stateVideo == 'pause'"
            @click="play"
          >
            <svg viewBox="0 0 494.148 494.148">
              <path
                fill="currentcolor"
                d="M405.284,201.188L130.804,13.28C118.128,4.596,105.356,0,94.74,0C74.216,0,61.52,16.472,61.52,44.044v406.124
                c0,27.54,12.68,43.98,33.156,43.98c10.632,0,23.2-4.6,35.904-13.308l274.608-187.904c17.66-12.104,27.44-28.392,27.44-45.884
                C432.632,229.572,422.964,213.288,405.284,201.188z"
              />
            </svg>
          </button>
          <button
            v-else
            @click="pause"
          >
            <svg viewBox="0 0 512 512">
              <path
                fill="currentcolor"
                d="M181.333,0H74.667c-17.643,0-32,14.357-32,32v448c0,17.643,14.357,32,32,32h106.667c17.643,0,32-14.357,32-32V32
                C213.333,14.357,198.976,0,181.333,0z"
              />
              <path
                fill="currentcolor"
                d="M437.333,0H330.667c-17.643,0-32,14.357-32,32v448c0,17.643,14.357,32,32,32h106.667c17.643,0,32-14.357,32-32V32
                C469.333,14.357,454.976,0,437.333,0z"
              />
            </svg>
          </button>
          <button @click="stop">
            <svg viewBox="0 0 493.56 493.56">
              <path
                fill="currentcolor"
                d="M438.254,0H58.974C27.502,0,0.006,25.992,0.006,57.472v379.256c0,31.48,27.496,56.832,58.968,56.832h379.28
                c31.468,0,55.3-25.352,55.3-56.832V57.472C493.554,25.992,469.722,0,438.254,0z"
              />
            </svg>
          </button>
          <button @click="back">
            <svg viewBox="0 0 13.68 13.68">
              <path
                fill="currentcolor"
                d="M13.268,1.662c-0.247-0.128-0.548-0.106-0.775,0.06L7.706,5.197V3.946V2.329
                c0-0.283-0.159-0.538-0.411-0.667c-0.248-0.128-0.549-0.106-0.776,0.06L0.306,6.233C0.115,6.374,0,6.598,0,6.838
                s0.114,0.465,0.306,0.604l6.213,4.512c0.128,0.094,0.283,0.145,0.439,0.145c0.114,0,0.23-0.03,0.337-0.083
                c0.252-0.129,0.411-0.388,0.411-0.665V9.732V8.478l4.787,3.477c0.129,0.094,0.283,0.145,0.439,0.145
                c0.113,0,0.229-0.03,0.336-0.083c0.253-0.129,0.412-0.388,0.412-0.665V9.733V3.947V2.329C13.68,2.047,13.521,1.791,13.268,1.662z"
              />
            </svg>
          </button>
          <button @click="forward">
            <svg viewBox="0 0 18.909 18.909">
              <path
                fill="currentcolor"
                d="M10.193,8.311L1.887,1.714C1.484,1.511,1.003,1.533,0.619,1.766C0.233,1.998,0,2.412,0,2.856v13.198
                c0,0.443,0.233,0.856,0.619,1.089c0.208,0.126,0.444,0.19,0.683,0.19c0.201,0,0.401-0.046,0.586-0.138l8.306-6.599
                c0.4-0.376,0.716-0.658,0.716-1.143S10.641,8.707,10.193,8.311z"
              />
              <path
                fill="currentcolor"
                d="M18.193,8.311L9.887,1.714C9.484,1.511,9.002,1.533,8.618,1.766
                c-0.386,0.232-0.619,0.646-0.619,1.09v13.198c0,0.443,0.233,0.856,0.619,1.089c0.208,0.126,0.444,0.19,0.683,0.19
                c0.201,0,0.401-0.046,0.586-0.138l8.306-6.599c0.4-0.376,0.716-0.658,0.716-1.143S18.641,8.707,18.193,8.311z"
              />
            </svg>
          </button>

          <span>{{ timer }}/{{ videoDuration }}</span>

          <span class="videoplayer__sound">
            <svg
              viewBox="0 0 384 384"
              @click="volumeMuted"
            >
              <path
                fill="currentcolor"
                d="M288,192c0-37.653-21.76-70.187-53.333-85.867v171.84C266.24,262.187,288,229.653,288,192z"
              />
              <polygon
                fill="currentcolor"
                points="0,128 0,256 85.333,256 192,362.667 192,21.333 85.333,128       "
              />
              <path
                fill="currentcolor"
                d="M234.667,4.907V48.96C296.32,67.307,341.333,124.373,341.333,192S296.32,316.693,234.667,335.04v44.053
                  C320.107,359.68,384,283.413,384,192S320.107,24.32,234.667,4.907z"
              />
            </svg>
            <input
              v-model.number="volumeNum"
              type="range"
              class="volume__input"
              min="0"
              max="100"
              @input="volume"
            >
            <span>{{ volumeNum }}%</span>
          </span>
        </div>
        <div class="videoplayer__btn-right">
          <button @click="speedUp">
            Скорость + 0.5
          </button>
          <button @click="speedDown">
            Скорость - 0.5
          </button>
          <button @click="speedNormal">
            Нормальная скорость
          </button>
          <span class="videoplayer__quality">
            {{ quality }}p
            <div class="videoplayer__quality-hover">
              <p @click="quality = 320">320p</p>
              <p @click="quality = 480">480p</p>
              <p @click="quality = 720">720p</p>
            </div>
          </span>
          <button
            v-if="!stateFullScreen"
            @click="fullScreen"
          >
            <svg viewBox="0 0 512 512">
              <path
                fill="currentcolor"
                d="M128,32V0H16C7.163,0,0,7.163,0,16v112h32V54.56L180.64,203.2l22.56-22.56L54.56,32H128z"
              />
              <path
                fill="currentcolor"
                d="M496,0H384v32h73.44L308.8,180.64l22.56,22.56L480,54.56V128h32V16C512,7.163,504.837,0,496,0z"
              />
              <path
                fill="currentcolor"
                d="M480,457.44L331.36,308.8l-22.56,22.56L457.44,480H384v32h112c8.837,0,16-7.163,16-16V384h-32V457.44z"
              />
              <path
                fill="currentcolor"
                d="M180.64,308.64L32,457.44V384H0v112c0,8.837,7.163,16,16,16h112v-32H54.56L203.2,331.36L180.64,308.64z"
              />
            </svg>
          </button>
          <button
            v-else
            @click="noneFullScreen"
          >
            <svg viewBox="0 0 357 357">
              <path
                fill="currentcolor"
                d="M0,280.5h76.5V357h51V229.5H0V280.5z M76.5,76.5H0v51h127.5V0h-51V76.5z M229.5,357h51v-76.5H357v-51H229.5V357z
                M280.5,76.5V0h-51v127.5H357v-51H280.5z"
              />
            </svg>
          </button>
          <div
            v-if="!our"
            class="logo"
          >
            logo
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'VideoPlayer',
  props: ['our'],
  data: () => ({
    video: NaN,
    videoPreview: null,
    previewTime: '0:00',
    videoDuration: '0:00',
    timer: '0:00',
    volumeNum: 50,
    progress: 0,
    quality: 320 && 480 && 720,
    stateVideo: 'stopped',
    stateFullScreen: false,
    loading: true
  }),
  watch: {
    // Изменение видео контейнера при изменение качества
    quality () {
      this.video = this.$refs.videoPlayer
      this.video.ontimeupdate = this.progressUpdate
      this.video.load()
    }
  },
  mounted () {
    // Загружем основное видео
    this.video = this.$refs.videoPlayer
    this.video.ontimeupdate = this.progressUpdate
    this.video.load()

    // Загружем превью видео
    this.videoPreview = this.$refs.videoPlayertPreview
    this.videoPreview.load()

    // Выключаем loader
    setInterval(() => {
      if (!isNaN(this.video)) {
        this.loading = true
      } else {
        this.videoDuration = this.filterTimer(this.video.duration)
        this.loading = false
      }
    }, 1000)
  },
  methods: {
    play () {
      this.video.play()
      this.stateVideo = 'play'
      setInterval(() => {
        this.timer = this.filterTimer(this.video.currentTime)
      }, 100)
    },
    pause () {
      this.video.pause()
      this.stateVideo = 'pause'
    },
    stop () {
      this.video.pause()
      this.video.currentTime = 0
      this.stateVideo = 'stopped'
    },
    speedUp () {
      this.video.playbackRate = 1.5
    },
    speedDown () {
      this.video.playbackRate = 0.5
    },
    speedNormal () {
      this.video.playbackRate = 1
    },
    volume () {
      const v = this.volumeNum
      this.video.volume = v / 100
    },
    volumeMuted () {
      const v = this.volumeNum = 0
      this.video.volume = v / 100
    },
    filterTimer (time) {
      const hr = ~~(time / 3600)
      const min = ~~((time % 3600) / 60)
      const sec = time % 60
      let secMin = ''
      if (hr > 0) {
        secMin += '' + hr + ':' + (min < 10 ? '0' : '')
      }
      secMin += '' + min + ':' + (sec < 10 ? '0' : '')
      secMin += '' + Math.round(sec)
      toString(secMin)

      return secMin
    },
    progressUpdate () {
      const video = this.video
      const d = video.duration
      const c = video.currentTime

      if (isNaN(d)) {
        this.progress = 0
      } else {
        this.progress = (100 * c) / d
      }
    },
    videoRewind () {
      const p = this.$refs.progress
      const w = p.offsetWidth
      const o = event.offsetX
      const video = this.video

      this.progress = 100 * o / w // Вычисление прогресса
      video.currentTime = video.duration * o / w // Вычисление момента перемотки
    },
    forward () {
      this.video.currentTime = this.video.currentTime + 15
    },
    back () {
      this.video.currentTime = this.video.currentTime - 15
    },
    videoPlayertPreview () {
      const video = this.videoPreview
      const p = this.$refs.progress
      const w = p.offsetWidth
      const o = event.offsetX
      video.currentTime = (video.duration * o) / w // Вычисление момент видео
      this.previewTime = this.filterTimer(video.currentTime)
    },
    fullScreen () {
      const videoContainer = this.$refs.videoContainer

      if (videoContainer.mozRequestFullScreen) {
        videoContainer.mozRequestFullScreen() // Разворачиваем для Firefox
      } else if (videoContainer.webkitRequestFullScreen) {
        videoContainer.webkitRequestFullScreen() // Разворачиваем для Chrome и Safari
      }

      this.stateFullScreen = true
    },
    noneFullScreen () {
      // Определяем факт полноэкранного отбражения какого-либо контейнера.
      if (document.mozFullScreenElement || document.webkitIsFullScreen) {
        if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen() // Сворачиваем для Mozilla
        } else if (document.webkitCancelFullScreen) {
          document.webkitCancelFullScreen() // Сворачиваем для Chrome и Safari
        }
      }

      this.stateFullScreen = false
    }
  }

}
</script>

<style scoped lang="less">
.img{
  max-width: 100%;
  display: block;
}

.show{
  display: none;
}

.videoplayer{
  position: relative;
  max-width: 100%;
  max-height: 100%;
  width: 100%;
  height: 100%;
  overflow: hidden;

  &.play{
    .videobar__progress{
      height: 3px;
    }

    .videobar__container{
      padding: 0;
      bottom: 0px;

      &:hover{
        padding: 4px 0;
        .videobar__progress{
          height: 10px;
        }
      }
    }
    .videoplayer__btn{
      opacity: 0;
    }

    .videoplayer__title{
      opacity: 0;
      visibility: hidden;
    }

    &:hover{
      .videoplayer__btn{
        opacity: 1;
      }

      .videobar__container{
        padding: 4px 0;
        bottom: 45px;
        .videobar__progress{
          height: 5px;
        }
      }

      .videoplayer__title{
        visibility: visible;
        opacity: 1;
      }
    }
  }

}

.videoplayer__title{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  padding: 20px 10px;
  text-align: left;
  background-color: fade(#000, 40%);
  box-shadow: 0 3px 10px fade(#000, 40%);
  transition: .2s ease;

  h2{
    margin: 0;
  }
}

.videoplayer__video{
  width: 100%;
  height: 100%;

  &--preview{
    position: absolute;
    bottom: 70px;
    left: 50%;
    transform: translateX(-50%);
    display: block;
    opacity: 0;
    z-index: 10;
    width: 200px;
    height: auto;
    border: 2px solid fade(#fff, 50%);
    border-radius: 10px;
    overflow: hidden;
    transition: .2s ease;
  }
}

.videoplayer__time{
    position: absolute;
    bottom: 80px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 11;
    background-color: fade(#000, 70%);
    padding: 5px 10px;
    font-size: 15px;
    visibility: hidden;
}

.videobar__container{
  position: absolute;
  bottom: 45px;
  z-index: 10;
  width: 100%;
  padding: 4px 0;
  background-color: fade(#000, 40%);
  transition: .2s ease;
   &:hover ~ .videoplayer__video--preview{
      opacity: 1;
   }

   &:hover ~ .videoplayer__time{
      visibility: visible;
   }

   &:hover{
     .videobar__progress{
       height: 10px;
     }
   }

}

.videobar__progress{
  height: 5px;
  background-color: goldenrod;
  border-radius: 30px;
  transition: .2s ease;
}

.videoplayer__btn{
  display: flex;
  height: 40px;
  line-height: 40px;
  width: 100%;
  text-align: left;
  position: absolute;
  bottom: 5px;
  left: 0;
  z-index: 10;
  background-color: fade(#000, 40%);
  box-shadow: 0 -3px 10px fade(#000, 40%);
  align-content: center;
  align-items: center;
  justify-content: space-between;
  transition: .2s ease;

  &-left{
    display: inline-flex;
    padding: 0 10px;
    height: 100%;
    align-content: center;
    align-items: center;

    button{
      background: none;
      border: none;
      color: #cecece;
      text-align: center;
      padding: 5px 5px;
      margin: 0 5px;
      cursor: pointer;
      transition: .2s ease;

      svg{
        width: 20px;
        height: 20px;
      }

      &:hover{
        color: #ffffff;
      }
    }
  }
  &-right{
    display: inline-flex;
    padding: 0 10px;
    height: 100%;
    align-content: center;
    align-items: center;
    justify-content: flex-end;
    margin-right: 0;

    button{
      background: none;
      border: none;
      color: #cecece;
      text-align: center;
      padding: 5px 5px;
      margin: 0 5px;
      cursor: pointer;
      transition: .2s ease;

      svg{
        width: 20px;
        height: 20px;
      }

      &:hover{
        color: #ffffff;
      }
    }
  }
}

.videoplayer__sound{
  display: flex;
  color: #cecece;
  align-content: center;
  align-items: center;
  padding: 0 10px;

  svg{
    width: 20px;
    height: 20px;
    margin-right: 5px;
  }

  &:hover{
    .volume__input{
      width: 100px;
      opacity: 1;
    }
  }
}

.volume__input{
  display: inline-block;
  width: 0;
  opacity: 0;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  margin-left: 70px;
  transition: .2s ease;
}

.videoplayer__quality{
  position: relative;
  margin: 0 5px;

  &:hover{
    .videoplayer__quality-hover{
      opacity: 1;
      height: 90px;
      visibility: visible;
    }
  }

  &-hover{
    position: absolute;
    top: -90px;
    left: -60%;
    opacity: 0;
    z-index: 10;
    width: 100px;
    height: 0;
    text-align: center;
    line-height: 30px;
    background-color: fade(#000, 70%);
    visibility: hidden;
    transition: .2s ease;

    p{
      margin: 0;
      padding: 0;

      &:hover{
        cursor: pointer;
        background-color: fade(#000, 80%);
      }
    }
  }
}

.logo{
  display: inline-block;
  font-size: 15px;
  margin-left: 5px;
}

</style>
