<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>Rocket Vue Test</h1>
    <div class="layout" ref="layout">
      <calculate
        v-model="item.result"
        v-for="item of calculators"
        :id="item.id"
        :key="item.id"
        @new="pressEnter"
        @start="dragStart"
        @over="dragOver"
        @end="dragEnd"/>
      <div v-if="dragging" class="calculate">
        <input @dragover.prevent @drop="dragFinish" type="number" style="opacity: .5" readonly />
      </div>
    </div>
  </div>
</template>

<script>
import Calculate from './components/Calculate'

export default {
  name: 'app',
  components: {
    Calculate
  },
  data: () => ({
    calculators: [{ id: 1, result: null }],
    dragging: false,
    result: null
  }),
  methods: {
    dragStart() {
      this.dragging = true;
    },
    dragOver(result) {
      this.result = result;
    },
    dragEnd() {
      this.dragging = false;
    },
    dragFinish() {
      this.calculators.push({ id: this.calculators.length + 1, result: this.result });
      setTimeout(() => {
        this.$refs.layout.lastElementChild.querySelector('input').focus()
      })
    },
    pressEnter(result) {
      this.calculators.push({ id: this.calculators.length + 1, result: result });
      setTimeout(() => {
        this.$refs.layout.lastElementChild.querySelector('input').focus()
      })
    }
  }
}
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 1440px;
  margin: 60px auto
}
.layout {
  display: flex;
  flex-direction: column;
}
</style>
