<template>
  <div class="map">
    <canvas id="canvasBG" width="1920" height="750"></canvas>
  </div>
</template>

<script>
  export default {
    name: '',
    created() {
    },
    mounted() {
      this.handleCanvasBG()
      this.handleCreateCanvas()
    },
    data() {
      return {
        mapCoordinateList: [ { x: 10, y: 10 }, { x: 60, y: 40 }, { x: 645, y: 45, }, { x: 650, y: 50, }, { x: 650, y: 300 }, { x: 300, y: 350 },  { x: 250,  y: 400 }, { x: 250, y: 600, },  { x: 300, y: 650 }, { x: 600, y: 650 }, { x: 600, y: 700 }, { x: 10, y: 700 }, ],
        mapIndex: 0,
        afterLaugl: 0,
        moveLen: 0,
      }
    },
    methods: {
      handleCreateCanvas() {
        this.moveLen = this.mapCoordinateList.length
        const map = document.querySelector(".map")
        const myCanvas = document.createElement('canvas')
        myCanvas.className = 'canvas'
        myCanvas.width = 1920
        myCanvas.height = 750
        myCanvas.style.position = 'absolute'
        myCanvas.style.left = 0
        myCanvas.style.top = 0
        myCanvas.style.backgroundColor = 'rgba(0,0,0,0)'
        myCanvas.style.Zindex = 2
        map.appendChild(myCanvas)
        const ctx = myCanvas.getContext('2d')
        ctx.beginPath()
        this.handleMove(ctx, map, myCanvas)
      },
      handleMove(ctx) {
        if(this.mapIndex) {
          const valx = this.mapCoordinateList[this.mapIndex].x - this.mapCoordinateList[this.mapIndex-1].x
          const valy = this.mapCoordinateList[this.mapIndex].y - this.mapCoordinateList[this.mapIndex-1].y
          const len = Math.sqrt(valx*valx+valy*valy)
          ctx.translate(len, 0)
        } else {
          ctx.translate(this.mapCoordinateList[this.mapIndex].x, this.mapCoordinateList[this.mapIndex].y)
        }
        const valx = this.mapCoordinateList[this.mapIndex+1].x - this.mapCoordinateList[this.mapIndex].x
        const valy = this.mapCoordinateList[this.mapIndex+1].y - this.mapCoordinateList[this.mapIndex].y
        let laugl = this.handleGetGugle(valx, valy)
        let moveIndex = 0
        const len = Math.sqrt(valx*valx+valy*valy)
        const lauglLen = Math.floor((laugl - this.afterLaugl) * 100)
        const surplus = laugl - this.afterLaugl - lauglLen / 100
        let index = 0
        const lauglTime = setInterval(() => {
          ctx.clearRect(-11, -6, 1920, 750)
          if(index < Math.abs(lauglLen)) {
            ctx.strokeRect( - 10, -5, 20, 10)
            if(lauglLen > 0) {
              ctx.rotate(0.01)
            } else {
              ctx.rotate(-0.01)
            }
            index++
          } else {
            ctx.rotate(surplus)
            clearInterval(lauglTime)
            const timer = setInterval(() => {
              ctx.clearRect(-11, -6, 1920, 750)
              ctx.strokeRect(moveIndex - 10, -5, 20, 10)
              ctx.stroke()
              if(moveIndex < len) {
                moveIndex += 1
              } else {
                clearInterval(timer)
                if(this.mapIndex > this.moveLen ) {
                  this.mapIndex = 1
                  this.mapCoordinateList.splice(0, this.moveLen)
                }
                if(this.mapIndex < this.mapCoordinateList.length - 2){
                  this.mapIndex++
                  this.handleMove(ctx)
                } else {
                  this.mapCoordinateList.push(...this.mapCoordinateList)
                  this.mapIndex++
                  this.handleMove(ctx)
                }
              }
            }, 10)
            moveIndex++
          }
        }, 10)
        this.afterLaugl = laugl
      },
      handleCanvasBG() {
        const myContext = document.getElementById('canvasBG')
        const ctx = myContext.getContext('2d')
        ctx.beginPath()
        this.mapCoordinateList.forEach((item, index) => {
          if(index+1 < this.mapCoordinateList.length) {
            ctx.moveTo(item.x, item.y)
            ctx.lineTo(this.mapCoordinateList[index+1].x, this.mapCoordinateList[index+1].y)
          } else {
            ctx.moveTo(item.x, item.y)
            ctx.lineTo(this.mapCoordinateList[0].x, this.mapCoordinateList[0].y)
          }
        })
        ctx.stroke()
      },
      handleGetGugle(valx, valy) {
        let laugl = 0
        if(valx > 0) {
          if(valy >= 0 ) {
            laugl = Math.atan(valy / valx)
          } else {
            laugl = Math.atan(Math.abs(valx / valy)) + 1.5*Math.PI
          }
        } else if(valx < 0) {
          if(valy > 0) {
            laugl = Math.atan(Math.abs(valx / valy)) + 0.5*Math.PI
          } else if(valy < 0) {
            laugl = Math.atan(valy / valx) + Math.PI
          } else {
            laugl = Math.PI
          }
        } else {
          if (valy > 0) {
            laugl = Math.PI*0.5
          } else {
            laugl = Math.PI*1.5
          }
        }
        return laugl
      }
    }
  }
</script>

<style lang="scss" scoped>
.map{
  position: relative;
  .canvasBG{
    z-index: 1;
  }
}
</style>
