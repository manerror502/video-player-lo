<template>
  <div>
    <p
      :class="{show: !loading}"
      style="color: black"
    >
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
        loop
        :src="'video.'+quality+'.mp4'"
        class="videoplayer__video"
        @ended="onEndFunc"
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
        v-if="our"
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
          <button
            class="videoplayer__btn-settings"
            :class="{active: settingsTooltip}"
          >
            <div
              class="settings__icon"
              @click="settingsTooltip = !settingsTooltip"
            >
              <svg viewBox="0 0 512 512">
                <path
                  fill="#d2d2d2"
                  d="m499.953125 197.703125-39.351563-8.554687c-3.421874-10.476563-7.660156-20.695313-12.664062-30.539063l21.785156-33.886719c3.890625-6.054687 3.035156-14.003906-2.050781-19.089844l-61.304687-61.304687c-5.085938-5.085937-13.035157-5.941406-19.089844-2.050781l-33.886719 21.785156c-9.84375-5.003906-20.0625-9.242188-30.539063-12.664062l-8.554687-39.351563c-1.527344-7.03125-7.753906-12.046875-14.949219-12.046875h-86.695312c-7.195313 0-13.421875 5.015625-14.949219 12.046875l-8.554687 39.351563c-10.476563 3.421874-20.695313 7.660156-30.539063 12.664062l-33.886719-21.785156c-6.054687-3.890625-14.003906-3.035156-19.089844 2.050781l-61.304687 61.304687c-5.085937 5.085938-5.941406 13.035157-2.050781 19.089844l21.785156 33.886719c-5.003906 9.84375-9.242188 20.0625-12.664062 30.539063l-39.351563 8.554687c-7.03125 1.53125-12.046875 7.753906-12.046875 14.949219v86.695312c0 7.195313 5.015625 13.417969 12.046875 14.949219l39.351563 8.554687c3.421874 10.476563 7.660156 20.695313 12.664062 30.539063l-21.785156 33.886719c-3.890625 6.054687-3.035156 14.003906 2.050781 19.089844l61.304687 61.304687c5.085938 5.085937 13.035157 5.941406 19.089844 2.050781l33.886719-21.785156c9.84375 5.003906 20.0625 9.242188 30.539063 12.664062l8.554687 39.351563c1.527344 7.03125 7.753906 12.046875 14.949219 12.046875h86.695312c7.195313 0 13.421875-5.015625 14.949219-12.046875l8.554687-39.351563c10.476563-3.421874 20.695313-7.660156 30.539063-12.664062l33.886719 21.785156c6.054687 3.890625 14.003906 3.039063 19.089844-2.050781l61.304687-61.304687c5.085937-5.085938 5.941406-13.035157 2.050781-19.089844l-21.785156-33.886719c5.003906-9.84375 9.242188-20.0625 12.664062-30.539063l39.351563-8.554687c7.03125-1.53125 12.046875-7.753906 12.046875-14.949219v-86.695312c0-7.195313-5.015625-13.417969-12.046875-14.949219zm-152.160156 58.296875c0 50.613281-41.179688 91.792969-91.792969 91.792969s-91.792969-41.179688-91.792969-91.792969 41.179688-91.792969 91.792969-91.792969 91.792969 41.179688 91.792969 91.792969zm0 0"
                />
              </svg>
            </div>

            <div
              v-if="settingsTooltip"
              class="settings__tooltip"
            >
              <li @click="selectTooltipQuality = !selectTooltipQuality, selectTooltipTime = false">
                Качество: <span>{{ quality }}p <span class="arrow"> ></span></span>

                <div
                  v-if="selectTooltipQuality"
                  class="settings__tooltip-select"
                >
                  <li @click="quality = 320, settingsTooltip = false">
                    320p
                  </li>
                  <li @click="quality = 480, settingsTooltip = false">
                    480p
                  </li>
                  <li @click="quality = 720, settingsTooltip = false">
                    720p
                  </li>
                </div>
              </li>

              <li @click="selectTooltipTime = !selectTooltipTime, selectTooltipQuality = false">
                Скорость: <span>{{ speed }} <span class="arrow"> ></span></span>
                <div
                  v-if="selectTooltipTime"
                  class="settings__tooltip-select"
                >
                  <li @click="speedUp, settingsTooltip = false">
                    Высокая
                  </li>
                  <li @click="speedDown, settingsTooltip = false">
                    Низкая
                  </li>
                  <li @click="speedNormal, settingsTooltip = false">
                    Нормальная
                  </li>
                </div>
              </li>
            </div>
          </button>

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
    speed: 'Нормальная',
    stateVideo: 'stopped',
    stateFullScreen: false,
    settingsTooltip: false,
    selectTooltipQuality: false,
    selectTooltipTime: false,
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
      this.speed = 'Высокая'
    },
    speedDown () {
      this.video.playbackRate = 0.5
      this.speed = 'Низкая'
    },
    speedNormal () {
      this.video.playbackRate = 1
      this.speed = 'Нормальная'
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
    onEndFunc () {
      this.video.currentTime = 0
      this.stateVideo = 'stopped'
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

.arrow{
  color: #ffffff;
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
      height: 0;
    }

    .videobar__container{
      padding: 0;
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
        padding: 1px 0;
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
  background: linear-gradient(180deg, rgba(0,0,0,0.6) 0%, rgba(240,240,240,0) 100%);
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
  background-color: fade(#b2b2b2, 40%);
  padding: 1px 0;
  transition: .5s ease;
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
  background: linear-gradient(0deg, rgba(0,0,0,0.7) 0%, rgba(240,240,240,0) 100%);
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
  cursor: pointer;

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

.videoplayer__btn-settings{
  position: relative;

  &:focus{
    outline: none;
  }

  svg{
    transition: .2s ease;
  }

  &.active{
    svg{
      transform: rotate(180deg);
    }

    &:hover{
      color: #cecece;
    }
  }
}

.logo{
  display: inline-block;
  font-size: 15px;
  margin-left: 5px;
}

.settings__tooltip{
  display: flex;
  flex-direction: column;
  position: absolute;
  bottom: 150%;
  right: 100px;
  background-color: fade(#000, 79%);
  padding: 10px 0;
  width: 270px;
  border-radius: 10px;

  li{
    display: flex;
    justify-content: space-between;
    list-style: none;
    width: 100%;
    margin: 0;
    padding: 10px 15px;
    text-align: left;
    font-size: 1.3rem;
    overflow: hidden;
    transition: .2s ease;
    span{
      text-align: right;
    }

    &:hover{
      color: #ffffff;
      background-color: fade(#000, 99%);
    }
  }

  &-select{
    position: absolute;
    top: 0px;
    right: -56%;
    opacity: 1;
    z-index: 10;
    width: 150px;
    text-align: center;
    line-height: 30px;
    background-color: fade(#000, 70%);
    border-radius: 10px;
    overflow: hidden;
    transition: .2s ease;

    li{
      display: block;
      list-style: none;
      margin: 0;
      padding: 5px 10px;
      text-align: center;

      &:hover{
        cursor: pointer;
        background-color: fade(#000, 80%);
      }
    }
  }

}

</style>
