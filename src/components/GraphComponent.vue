<template>
  <div class="card-container">
    <div class="card">
      <div class="border"></div>
      <div class="content">
        <h1>Sine Graph</h1>
        <div class="graph-container" @wheel.prevent="handleScroll">
          <canvas ref="sineGraphCanvas"></canvas>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';

export default {
  name: 'GraphComponent',
  data() {
    return {
      sineGraphCanvas: null,
      chartInstance: null,
      currentScrollX: 0,
      totalPoints: 36000,
    };
  },
  computed: {
    pointsToDisplay() {
      return Math.min(this.totalPoints, 1000);
    },
  },
  mounted() {
    this.sineGraphCanvas = this.$refs.sineGraphCanvas;
    this.chartInstance = this.drawSineGraph();
  },
  methods: {
    drawSineGraph() {
      const ctx = this.sineGraphCanvas.getContext('2d');
      const data = {
        labels: Array.from({ length: this.totalPoints }, (_, i) => i),
        datasets: [
          {
            label: 'Sine Graph',
            data: Array.from({ length: this.totalPoints }, (_, i) => Math.sin((i * Math.PI) / 180)),
            borderColor: 'blue',
            borderWidth: 0.5,
            fill: false,
          },
        ],
      };
      const options = {
        scales: {
          x: {
            type: 'linear',
            position: 'bottom',
            min: 0,
            max: this.pointsToDisplay,
            ticks: {
              maxRotation: 0,
            },
          },
          y: {
            type: 'linear',
            position: 'left',
            min: -1.5,
            max: 1.5,
          },
        },
      };
      return new Chart(ctx, {
        type: 'line',
        data,
        options,
      });
    },
    handleScroll(event) {
      const delta = Math.max(-1, Math.min(1, (event.deltaY || -event.detail)));
      console.log("delta: " + delta);
      if (event.deltaY) console.log("deltaY: " + event.deltaY);
      if (event.detail) console.log("detail: " + event.detail);
      this.currentScrollX += delta * 50; // speed
      const maxScrollX = this.totalPoints - this.pointsToDisplay;
      this.currentScrollX = Math.max(0, Math.min(this.currentScrollX, maxScrollX));

      if (this.chartInstance) {
        console.log(this.chartInstance.options);
        this.chartInstance.options.scales.x.min = this.currentScrollX;
        this.chartInstance.options.scales.x.max = this.currentScrollX + this.pointsToDisplay;
        this.chartInstance.update();
      }
      event.preventDefault();
    },
  },
};
</script>

<style scoped>
.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 81vh;
}

.card {
  position: relative;
  width: 50vw;
  height: 71vh;
  background-color: #ffffff;
  border-radius: 10px;
  overflow: hidden;
}

.border {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border: 2px solid #ccc;
  pointer-events: none;
}

.content {
  padding: 20px;
}

.graph-container {
  overflow: hidden;
  position: relative;
  height: 100%;
}

h1 {
  margin-bottom: 10px;
}

p {
  margin: 0;
}
</style>