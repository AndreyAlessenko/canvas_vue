<template>
  <div>
    <canvas ref="myCanvas"
            :width="canvasSize"
            :height="canvasSize"
            @mousedown="startDragging"
            @mouseup="stopDragging"
            @mousemove="moveSquare"></canvas>
    <div>
      <label>Радиус окружности:</label>
      <input
          type="number"
          :value="radius"
          @change="radiusChange"
          min="1"
          step="1"
      />
    </div>
    <div>
      <label>Размер стороны квадрата:</label>
      <input
          type="number"
          :value="squareSize"
          @change="squareSizeChange"
          min="1"
          step="1"
      />
    </div>
    <div>
      <label>X-координата квадрата:</label>
      <input
          type="number"
          :value="squareX"
          @change="squareXChange"
          :min="(-1) * (radius+squareSize/2)"
          :max="radius+squareSize/2"
          step="1"
      />
    </div>
    <div>
      <label>Y-координата квадрата:</label>
      <input

          type="number"
          :value="squareY"
          @change="squareYChange"
          :min="(-1) * (radius+squareSize/2)"
          :max="radius+squareSize/2"
          step="1"
      />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      canvasSize: 400,
      radius: 100,
      previousRadius: 100,
      squareSize: 20,
      squareX: 0,
      squareY: 10,
      isDragging: false,
    };
  },
  mounted() {
    this.draw();
  },
  methods: {
    radiusChange(event){
      const value = parseInt(event.target.value, 10);
      const distanceToCenter = Math.sqrt((this.squareX) ** 2 + ((this.squareY + this.squareSize/2) * (-1)) ** 2);
      if (!(
          isNaN(value)
          || value <= this.squareSize
          || value <= distanceToCenter
      )) {
        this.previousRadius = value
        this.radius = value;
        this.draw();
      }
    },
    squareSizeChange(event){
      const value = parseInt(event.target.value, 10);
      const distanceToCenter = Math.sqrt((this.squareX + value/2) ** 2 + ((this.squareY + value/2) * (-1)) ** 2);
      if (!(
          isNaN(value)
          || value >= this.radius
          || this.radius <= distanceToCenter
      )) {
        this.squareSize = value
        this.draw();
      }
    },
    squareXChange(event){
      const value = parseInt(event.target.value, 10);
      const distanceToCenter = Math.sqrt((Math.abs(value) + this.squareSize/2) ** 2 + ((Math.abs(this.squareY) + this.squareSize/2) * (-1)) ** 2);
      if(!(isNaN(value)
          || (Math.abs(value) >= this.radius-this.squareSize/2)
          || (this.radius <= distanceToCenter)
      )) {
        this.squareX = value
        this.draw();
      }

    },
    squareYChange(event){
      const value = parseInt(event.target.value, 10);
      const distanceToCenter = Math.sqrt((Math.abs(this.squareX) + this.squareSize/2) ** 2 + ((Math.abs(value) + this.squareSize/2) * (-1)) ** 2);

      if(!(isNaN(value)
          || (Math.abs(value) >= this.radius - this.squareSize/2)
          || (this.radius <= distanceToCenter)
      )) {
        this.squareY = value
        this.draw();
      }
    },
    draw() {

      const canvas = this.$refs.myCanvas;
      const ctx = canvas.getContext("2d");
      const centerX = this.canvasSize / 2;
      const centerY = this.canvasSize / 2;

      ctx.clearRect(0, 0, this.canvasSize, this.canvasSize);
      ctx.beginPath();
      ctx.arc(centerX, centerY, this.radius, 0, 2 * Math.PI);
      ctx.rect(this.squareX + centerX-this.squareSize/2, (-1)*(this.squareY + this.squareSize/2)  + centerY , this.squareSize, this.squareSize);

      ctx.stroke();

    },
    startDragging() {
      this.isDragging = true;
    },
    stopDragging() {
      this.isDragging = false;
    },
    moveSquare(event) {
      if (this.isDragging) {
        const centerX = this.canvasSize / 2;
        const centerY = this.canvasSize / 2;
        const distanceToCenter = Math.sqrt((event.offsetX - centerX) ** 2 + (event.offsetY - centerY) ** 2);
        const maxDistance = this.radius - this.squareSize / 2;
        if (distanceToCenter <= maxDistance) {
          this.squareX = event.offsetX - centerX;
          this.squareY = (-1)*(event.offsetY) + centerY ;
          this.draw();
        }
      }
    },
  },
};
</script>

<style>
canvas {
  border: 1px solid black;
}
</style>
