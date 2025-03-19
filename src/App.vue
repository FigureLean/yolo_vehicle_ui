<template>
  <div id="app">
    <DetectionPanel />
  </div>
</template>

<script>
import DetectionPanel from './components/DetectionPanel.vue';

export default {
  name: 'App',
  components: {
    DetectionPanel
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>