<template>
  <div>
    <div class="calculate" ref="calculate">
      <div class="item" v-for="(input, idx) of inputs" :key="input.id">
        <span class="operand" v-if="idx > 0">{{ input.operand }}</span>
        <input
          autofocus="autofocus"
          type="number"
          :draggable="!isEmpty && inputs.length > 1"
          :id="input.id"
          v-model="input.model"
          @dragstart="dragStart"
          @dragover="dragOver(input.model)"
          @dragend="dragEnd"
          @keydown="keyHandler"
          @keypress.enter="newCalc">
      </div>
      <div v-show="result || inputs.length > 1">
        <span class="operand">=</span>
        <input
          draggable="true"
          @dragstart="dragStart"
          @dragover="dragOver"
          @dragend="dragEnd"
          class="result"
          type="number"
          readonly
          disabled
          v-model="result">
      </div>
    </div>
    <div class="helper" v-if="isEmpty && inputs.length === 1">
      <small>Просто начните вводить числа и арифметические знаки...</small>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: Number,
      required: false,
      default: null
    }
  },
  data: () => ({
    inputs: [{ id: 1, operand: '+', model: null }],
    operations: {
      '+': { keyCode: 187, keyCodeNum: 107 },
      '-': { keyCode: 189, keyCodeNum: 109 },
      '*': { keyCode: 56, keyCodeNum: 106 },
      '/': { keyCode: 191, keyCodeNum: 111 }
    }
  }),
  created() {
    this.inputs[0].model = this.value
  },
  computed: {
    isEmpty() {
      return this.inputs.some(input => !input.model)
    },
    result() {
      if (this.inputs.length > 1) {
        try {
          return eval(this.inputs.map(input => input.operand + input.model).join(' '))
        } catch (e) {
          return eval(this.inputs.map(input => input.operand + input.model).join(' ').slice(0, -1))
        }
      }
      return 0
    }
  },
  methods: {
    keyHandler(e) {
      if (e.keyCode === 8) {
        if (this.isEmpty && this.inputs.length > 1) {
          this.inputs.pop();
          setTimeout(() => {
            this.$refs.calculate.lastElementChild.previousElementSibling.querySelector('input').focus();
          })
        }
      }
      for (const operand in this.operations) {
        if (this.operations.hasOwnProperty(operand)) {
          if ((e.keyCode === this.operations[operand].keyCode && e.shiftKey) || e.keyCode === this.operations[operand].keyCode || e.keyCode === this.operations[operand].keyCodeNum) {
            e.preventDefault();
            if (!this.isEmpty) {
              const id = this.inputs.length + 1;
              this.inputs.push({ id, operand, model: null });
              setTimeout(() => {
                this.$refs.calculate.lastElementChild.previousElementSibling.querySelector('input').focus();
              })
            }
          }
        }
      }
    },
    dragStart() {
      this.$emit('start');
    },
    dragOver(value) {
      this.$emit('over', typeof value === 'string' ? +value : this.result);
    },
    dragEnd() {
      this.$emit('end')
    },
    newCalc() {
      if (this.inputs.length > 1) {
        this.$emit('new', this.result)
      }
    }
  },
}
</script>

<style lang="scss">
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
input[type="number"] {
  -moz-appearance:textfield;
  background: #d8d8d8;
  border-radius: 15px;
  border: none;
  width: 55px;
  height: 40px;
  outline: none;
  font-size: 16px;
  text-align: center;
  font-weight: bold;
  margin: 0 15px;
}
.calculate {
  display: flex;
  padding: 10px 0;
  background: #f1f1f1;
}
.operand {
  font-size: 16px;
  font-weight: bold;
}
.helper {
  display: flex;
  margin: 10px;
}
.result {
  cursor: grabbing;
}
</style>
