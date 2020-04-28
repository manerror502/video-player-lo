<template>
  <div id="app">
    <button
      class="btn"
      @click="modal = true, our = true"
    >
      Открыть плеер 1!
    </button>
    <button
      class="btn"
      @click="modal = true, our = false"
    >
      Открыть плеер 2!
    </button>

    <ModalWindow
      v-if="modal"
      :our="our"
      @close="modal = false"
    />
  </div>
</template>

<script>

import ModalWindow from './components/ModalWindow.vue'

export default {
  name: 'App',
  components: {
    ModalWindow
  },
  data: () => ({
    our: true,
    modal: false
  })
}
</script>

<style lang="less">
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

html, body {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    margin: 0;
    margin-top: 60px;
    font-size: 15px;
    color: #dfe7ee;
    &.no-scroll {
        overflow: hidden;
    }
}

*,
*:before,
*:after {
    box-sizing: border-box;
}

h1,
h2,
h3,
h4,
h5,
h6 {
    margin: 0;
}

p {
    margin: 0;
}

.btn{
  color: #00c663;
  border: 1px solid #00c663;
  background: none;
  padding: 20px 30px;
  background-color: transparent;
  transition: .3s ease;
  border-radius: 10px;
  cursor: pointer;
  outline: #00c663;
  margin-left: 10px;

  &:hover{
    background-color: #00c663;
    color: #fff;
  }

}
</style>
