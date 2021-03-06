<template>
  <div class="numberPad" :class="`${show}`">
    <label class="notes">
      <span class="name">备注：</span>
      <input
        type="text"
        placeholder="点击输入备注"
        :value="value"
        @input="onValueChange($event.target.value)"
      />
      <div class="close" @click.prevent="hide">
        <Icon name="close"></Icon>
      </div>
    </label>
    <div class="buttons">
      <button @click="inputContent">1</button>
      <button @click="inputContent">2</button>
      <button @click="inputContent">3</button>
      <button @click="remove">删除</button>
      <button @click="inputContent">4</button>
      <button @click="inputContent">5</button>
      <button @click="inputContent">6</button>
      <button @click="add">+</button>
      <button @click="inputContent">7</button>
      <button @click="inputContent">8</button>
      <button @click="inputContent">9</button>
      <button @click="ok" class="ok">确认</button>
      <button @click="clear">清空</button>
      <button @click="inputContent" class="zero">0</button>
      <button @click="inputContent">.</button>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { Component, Prop, Watch } from "vue-property-decorator";

@Component
export default class NumberPad extends Vue {
  @Prop() readonly show!: string;
  @Prop() readonly amount!: string;
  @Prop() readonly notes!: string;
  output = this.amount.toString();
  value = this.notes;
  onValueChange(value: string) {
    this.$emit("update:notes", value);
  }
  hide() {
    this.$emit("update:hide");
  }
  inputContent(event: MouseEvent) {
    const button = event.target as HTMLButtonElement; //断言写法1
    const input = button.textContent!; // 断言写法2 断言它不是null
    if (this.output.length === 16) {
      return;
    }
    if (this.output === "0") {
      if ("0123456789".indexOf(input) >= 0) {
        this.output = input;
      } else {
        this.output += input;
      }
      this.$emit("update:amount", this.output);
      return;
    }
    if (this.output === "0.0") {
      if ("0".indexOf(input) >= 0) {
        this.output = input;
      } else {
        this.output += input;
      }
      this.$emit("update:amount", this.output);
      return;
    }
    if ("0123456789".indexOf(input) >= 0) {
      if (this.output.indexOf("+") < 0) {
        if (
          this.output.indexOf(".") >= 0 &&
          this.output.split(".")[1].length >= 2
        ) {
          return;
        }
      } else {
        const array = this.output.split("+");
        if (array[1].indexOf(".") >= 0 && array[1].split(".")[1].length >= 2) {
          return;
        }
      }
    }

    if (this.output.indexOf(".") >= 0 && input === ".") {
      if (this.output.indexOf("+") >= 0) {
        if (this.output.split("+")[1].indexOf(".") < 0) {
          this.output += input;
        }
      }
      this.$emit("update:amount", this.output);
      return;
    }
    this.output += input;
    this.$emit("update:amount", this.output);
  }
  remove() {
    if (this.output.length === 1) {
      this.output = "0";
    } else {
      this.output = this.output.slice(0, -1);
    }
    this.$emit("update:amount", this.output);
  }
  clear() {
    this.output = "0";
    this.$emit("update:amount", this.output);
  }
  add() {
    if (this.output.indexOf("+") >= 0) {
      const array = this.output.split("+");
      this.output = (parseFloat(array[0]) + parseFloat(array[1])).toString();
    }
    this.output = this.output + "+";
    this.$emit("update:amount", this.output);
  }
  ok() {
    if (this.output === "0" || this.output === "0.0") {
      this.hide();
      return;
    }
    let last = this.output.slice(-1);
    if (last === "+" || last === ".") {
      this.output = this.output.substring(0, this.output.length - 1);
    }
    if (this.output.indexOf("+") >= 0) {
      const array = this.output.split("+");
      this.output = (parseFloat(array[0]) + parseFloat(array[1])).toString();
    }
    this.$emit("update:amount", parseFloat(this.output));
    this.$emit("submit", parseFloat(this.output));
    this.output = "0";
  }
}
</script>

<style lang="scss" scoped>
@import "~@/assets/style/helper.scss";

.numberPad {
  background: #393960;
  color: white;
  width: 100%;
  height: 40vh;
  position: absolute;
  left: 0;
  bottom: -40vh;
  transition: all 0.3s;
  &.show {
    bottom: 8vh;
  }
  .notes {
    width: 100%;
    height: 8vh;
    font-size: 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    > .name {
      margin-left: 24px;
      margin-right: 8px;
      display: flex;
      justify-content: center;
    }
    input {
      display: inline-block;
      height: 40px;
      flex-grow: 1;
      background: transparent;
      border: none;
    }
    > .close {
      width: 20vw;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      .icon {
        width: 28px;
        height: 28px;
        color: white;
      }
    }
  }

  .output {
    @extend %clearFix;
    @extend %innerShadow;
    font-size: 36px;
    font-family: Consolas, monospace;
    padding: 9px 16px;
    text-align: right;
  }
  .buttons {
    @extend %clearFix;
    > button {
      width: 25%;
      height: 8vh;
      float: left;
      background: transparent;
      border: 1px solid #8d8ea6;
      &.ok {
        height: 16vh;
        background: #25c489;
        float: right;
      }
    }
  }
}
@media (max-width: 360px) {
  .numberPad > .notes > .name {
    width: 25vw;
    margin-left: 16px;
    margin-right: 0;
  }
  .numberPad > .notes > .close {
    margin-right: 8px;
    > .icon {
      width: 24px;
      height: 24px;
    }
  }
}
</style>
