<template>
  <div id="app">
    <button class="btn"  @click="modal = true, our = true" >Открыть плеер 1!</button>
    <button class="btn"  @click="modal = true, our = false" >Открыть плеер 2!</button>

    <ModalWindow v-if='modal' :our=our @close='modal = false' />
  </div>
</template>

<script>

import ModalWindow from './components/ModalWindow.vue'

export default {
  name: 'App',
  data: () => ({
    our: true,
    modal: false
  }),
  components: {
    ModalWindow
  }
}
</script>

<style lang="less">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #dfe7ee;
  margin-top: 60px;
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
