<template>
  <div id="app" ref="calculate">
    <div class="item" v-for="input of inputs" :key="input.id">
        <span>{{ input.operand }}</span>
        <input type="text" :id="input.id" v-model="model[input.id]" @keydown="keyHandler">
        <input v-if="result" type="text" readonly disabled v-model="result">
    </div>
    {{ model }}
  </div>
</template>

<script>

export default {
  name: 'app',
  data: () => ({
    model: {},
    result: 0,
    inputs: [{ id: Math.random(), operand: null }],
    operations: {
      '+': { keyCode: 187, keyCodeNum: 107 },
      '-': { keyCode: 189, keyCodeNum: 109 },
      '*': { keyCode: 56, keyCodeNum: 106 },
      '/': { keyCode: 191, keyCodeNum: 111 }
    }
  }),
  computed: {
    isEmpty() {
      return this.inputs.some(input => !this.model[input.id])
    }
  },
  methods: {
    keyHandler(e) {
      if (e.keyCode === 8) {
        if (this.isEmpty) {
          delete this.model[this.inputs.pop().id];
          setTimeout(() => {
            this.$refs.calculate.lastElementChild.querySelector('input').focus();
          })
        }
      }

      for (const operand in this.operations) {
        if (this.operations.hasOwnProperty(operand)) {
          if ((e.keyCode === this.operations[operand].keyCode && e.shiftKey) || e.keyCode === this.operations[operand].keyCodeNum) {
            e.preventDefault();
            if (!this.isEmpty) {
              const id = Math.random();
              this.inputs.push({ id, operand });
              setTimeout(() => {
                this.$refs.calculate.lastElementChild.querySelector('input').focus();
              })
            }
          }
        }
      }

      // [...this.$refs.calculate.children].reduce((a, b) => {
      //   const operand = b.querySelector('span');
      //   if (operand.innerText === '+') {
      //     this.result = this.addition(a.lastElementChild.value, b.lastElementChild.value)
      //   }
      // })
    },
    addition(a, b) {
      return a + b;
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
}
</style>
