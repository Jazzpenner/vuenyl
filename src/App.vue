<template>
  <Vinyl
      :tracks="tracks"
      :cover-url="'linkToSomething'"
      :imageWidth="105"
      :imageHeight="105"
      :vinyl-color="'white'"
  />
</template>

<script>
import Vinyl from "./components/Vinyl";

export default {
  name: 'App',
  components: {
    Vinyl
  },
  data() {
    return {
      tracks: {
        Track1: this.randomTrackLength(),
        Track2: this.randomTrackLength(),
        Track3: this.randomTrackLength(),
        Track4: this.randomTrackLength(),
        Track5: this.randomTrackLength(),
      },
    }
  },
  methods: {
    randomTrackLength() {
      const max = 300,
          min = 100
      return Math.random() * (max - min) + min;
    },
  }
}
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
