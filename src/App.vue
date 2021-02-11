<template>
  <div id="myapp">
    <canvas ref="pattern" id="patternLock"></canvas>
    <button @click="draw">Try</button>
  </div>
</template>

<script>
import {isEqual} from "lodash"
import PatternLockJS from "./pattern-lock/PatternLock"
export default {
  name: 'App',
  mounted() {
    this.$nextTick(() => {
      const elem = this.$refs.pattern
      this.patternLock = PatternLockJS({
        $canvas: elem,
        width: 300,
        height: 300
      });
      this.reset()
    })
  },
  data(){
    return {
      mappings: [
        [1, 1],
        [1, 2],
        [1, 3],
        [2, 1],
        [2, 2],
        [2, 3],
        [3, 1],
        [3, 2],
        [3, 3]
      ],
      patternLock: null
    }
  },
  methods: {
    convertXYToNumber(x, y) {
      return this.mappings.findIndex(co => co[0] === parseInt(x) && co[1] === parseInt(y)) + 1
    },
    convertNumberToXY(z) {
      const zn = parseInt(z) - 1
      return this.mappings[zn]
    },
    reset() {
      this.patternLock.reset()
      this.patternLock.on("complete", this.onPatternComplete);
    },
    draw() {
      const preFilledPassword = [ 7,4,5,6 ];
      this.reset()
      preFilledPassword.forEach((node, index) => {
        if(index < (preFilledPassword.length - 1)) {
          const [c1, r1] = this.convertNumberToXY(node)
          const [c2, r2] = this.convertNumberToXY(preFilledPassword[index + 1])
          this.patternLock.joinNodes(r1, c1, r2, c2)
        }
      })
      // match password here
      const isSame = isEqual(preFilledPassword, [7,4,5,6])
      console.log(isSame)
    },
    onPatternComplete(nodes) {
      try {
        console.clear()
        const password = JSON.parse(nodes.password).map(node => this.convertXYToNumber(node.col, node.row))
        console.log(password)
      }
      catch(e){
        console.error(e)
      }
    }
  }
}
</script>

<style>
#myapp {
  background: #333;
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}
</style>