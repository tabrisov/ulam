<template>
  <q-page>
    <div class="q-mt-xl text-center">
      <input
        v-model="size"
        type="number"
        min="3"
        max="201"
        step="2"
      />
    </div>

    <div class="map">
      <div
        class="row"
        v-for="i in size"
        :key="`row-${i}-of-${size}`"
      >
        <span
          v-for="j in size"
          :key="`cell-${i}-${j}-of-${size}`"
          class="cell"
          :class="{ 'cell--mark' : isSimple(getCell(i, j)) }"
        >
        </span>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted, watch } from 'vue';

export default defineComponent({
  name: 'PageIndex',

  setup: function() {
    const size = ref(123);
    const map = ref([]);

    const getCell = (x, y) => map.value[x * size.value + y]

    const setCell = (x, y, value) => {
      map.value[x * size.value + y] = value;
    }

    onMounted(() => {
      generateMap(size.value)
    })

    watch(size, (value) => {
      generateMap(value)
    })

    const generateMap = (n) => {
      let direction = 0 // 0 = right, 1 = up, 2 = left, 3 = down
      let segmentLimit = 1 // Left to print before changing direction
      let segmentCounter = 0 // How many printed in the current direction
      let turns = 0 // every second turn segmentLimit increses by 1

      let x = Math.floor(n/2) + 1
      let y = Math.floor(n/2) + 1

      for (let i = 1; i <= n * n; i++) {
        setCell(x, y, i)

        switch (direction) {
          case 0:
            x++ // move right
            break
            
          case 1:
            y-- // move up
            break
            
          case 2:
            x-- // move left
            break
            
          case 3:
            y++ // move down
            break
        }

        segmentCounter++

        if (segmentCounter >= segmentLimit) {
          segmentCounter = 0
          turns = (turns + 1) % 2
          direction = (direction + 1) % 4
              
          if (turns === 0) {
            segmentLimit++
          }
        }
      }
    }

    const isSimple = (x) => {
      let result=true;
      for(let i=3;i<x;i++) {
        if(x%i===0) {
          result=false;
          break;
        }
      }
      return result;
    }

    return {
      size,
      map,
      getCell,
      setCell,
      generateMap,
      isSimple
    }
  }
})
</script>

<style lang="sass">
$size : 4px
.map
  margin-top: 30px

.row
  display: flex
  justify-content: center
  flex-wrap: nowrap

.cell
  display: flex
  justify-content: center
  align-items: center
  width: $size
  height: $size
  flex-grow: 0
  flex-shrink: 0
  border-bottom: 0 none
  border-right: 0 none
  font-size: 10px
  color: #999

.cell--mark
  color: #ffc
  font-weight: bold
  background: #333
</style>
