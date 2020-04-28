<template>
  <div class="modal">
    <button
      class="btn btn-close"
      @click="$emit('close')"
    >
      X
    </button>
    <div class="modal__dialog">
      <div class="modal__player">
        <VideoPlayer :our="our" />
      </div>
    </div>
  </div>
</template>

<script>
import VideoPlayer from './VideoPlayer'

export default {
  name: 'ModalWindow',
  components: {
    VideoPlayer
  },
  props: {
    our: {
      type: Boolean,
      required: true
    }
  }
}
</script>

<style lang="less" scoped>
.modal{
  width: 100%;
  height: 100%;
  background-color: fade(#1b2d35, 90%);
  position: fixed;
  top: 0;
  left: 0;
  overflow: hidden;
  display: flex;
}

.modal__dialog{
  max-width: 1200px;
  width: 100%;
  min-height: 100px;
  padding: 15px;
  margin: auto;
  background-color: #d7d7d7;
  box-shadow: 0 0 5px fade(#000, 50%);
  border-radius: 20px;
}

.btn-close{
  position: absolute;
  right: 10px;
  top: 10px;
  z-index: 12;
  display: block;
  padding: 10px 20px;
  font-weight: bold;
}
</style>
