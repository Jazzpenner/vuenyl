<template>
  <div class="svg-spacing">
    <svg id="profile" :width="width" :height="height"
    >
      <defs>
        <filter id="shadow" filterUnits="userSpaceOnUse">
          <feOffset result="offOut" in="SourceAlpha" :dx="shadowOffset" :dy="shadowOffset"/>
          <feGaussianBlur result="blurOut" in="offOut" stdDeviation="7"/>
          <feBlend in="SourceGraphic" in2="blurOut" mode="normal"/>
        </filter>
        <clipPath id="circle-clip">
          <use href="#circle"/>
        </clipPath>
      </defs>

      <circle
          class="vinyl"
          :r="vinylRadius" :fill="vinylColor"
          stroke-width="1px"
          :stroke="borderColor"
          cx="50%"
          cy="50%"
          style="filter:url(#shadow);"
      />

      <g clip-path="url(#circle-clip)">
        <circle
            stroke-width="3px"
            :stroke="borderColor"
            id="circle"
            :r="coverRadius"
            fill="url(#image)"
            cx="50%"
            cy="50%"
        />
        <image
            :href="coverUrl"
            :x="width/2 -imageWidth/2"
            :y="height/2 -imageHeight/2"
            :width="imageWidth"
            :height="imageHeight"
            preserveAspectRatio="xMidYMid meet"

        />
        <use href="#circle" fill="none" stroke="#0F1C3F" stroke-width="2" opacity="0.25"/>
      </g>

      <circle r="7px" fill="#d6d6d8" stroke="#d6d6d810" cx="50%" cy="50%" style="filter:url(#shadow);"></circle>

      <g v-for="(track, trackInt) in Object.keys(tracks)" :key="track">
        <circle
            v-for="circle in Array(circlesPerTrack(track)).keys()" :key="circle"
            :r="circlePosition(circle, trackInt)"
            :stroke="grooveColor"
            :stroke-width="grooveWidth"
            fill="transparent"
            cx="50%"
            cy="50%"
        />
      </g>

    </svg>
  </div>
</template>

<script>
export default {
  name: "Vinyl",
  data() {
    return {
      width: 420,
      height: 420,
      borderColor: "rgba(0,0,0,0.4)",
      spaceTillFirstTrack: 5,
      grooveWidth: 1,
      grooveOffset: 1,
      grooveColor: "rgba(0,0,0,0.4)",

      spacingBetweenTracks: 2,
      spacingTrackCover: 20,

      shadowOffset: 4,
      // value to make vinyl svg fit even if shadow becomes to large because of blur
      // change it if you need to change the shadow
      shadowScaleCorrection: 3,

      hover: false,
      rotateVal: 0,
    }
  },
  props: {
    coverUrl: {
      type: String,
      required: false,
    },
    tracks: Object,
    imageWidth: Number,
    imageHeight: Number,
    vinylColor: String,
  },
  methods: {
    radiusForTrack(track) {
      return this.tracks[track] * this.trackRadiusTimeRatio - this.spacingBetweenTracks
    },
    circlesPerTrack(track) {
      return Math.floor(this.radiusForTrack(track) / (this.grooveWidth + this.grooveOffset))
    },
    circlePosition(circle, trackInt) {
      return this.trackStartPosition[trackInt] - (this.grooveWidth + this.grooveOffset) * circle
    }
  },
  computed: {
    coverRadius() {
      return this.width / 8
    },
    numTracks() {
      return Object.keys(this.tracks).length
    },
    vinylRadius() {
      return this.width / 2 - this.spaceTillFirstTrack - this.shadowOffset * this.shadowScaleCorrection
    },
    maxTrackLengthRadius() {
      return this.vinylRadius - this.coverRadius - this.spacingTrackCover
    },
    overallTime() {
      return Object.values(this.tracks).reduce((a, b) => a + b)
    },
    trackRadiusTimeRatio() {
      return this.maxTrackLengthRadius / this.overallTime
    },
    trackStartPosition() {
      const returnArray = []
      const spanOfTracks = Object.keys(this.tracks).map(x => this.radiusForTrack(x))
      for (let i = 0; i < spanOfTracks.length; i++) {
        if (i === 0) {
          returnArray[i] = this.vinylRadius - this.spaceTillFirstTrack
        } else {
          returnArray[i] = returnArray[i - 1] - spanOfTracks[i - 1] - this.spacingBetweenTracks
        }
      }
      return returnArray
    }

  },

}

</script>

<style scoped>
.svg-spacing {
  min-height: 100%;
}
</style>