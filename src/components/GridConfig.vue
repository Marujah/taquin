<script setup lang="ts">

const emit = defineEmits(['gridchanged']);
const checked = false;

function onGridChange() {
    let newGrid = checked ? 4 : 3
    emit('gridchanged', newGrid)
}
</script>

<template>
    <div class="grid-config">
      <div class="switch-container">
              <span class="switch-option grid-three">3x3</span>
              <label class="switch">
                  <input id="form-switcher" type="checkbox" v-model="checked" @change="onGridChange">
                  <div class="slider round"></div>
              </label>
              <span class="switch-option grid-four">4x4</span>
          </div>
    </div>
  </template>
  
  <style lang="less" scoped>
  .grid-config {
    padding: 5px;
    margin-bottom: 2em;
    .switch-option {
      display: inline-block;
      height: 34px;
      padding-top: 10px;
      margin: 0 0.25em;
      font-weight: 700;
      &.grid-three {
        color: #673ab7;
      }
      &.grid-four {
        color: #396add;
      }
    }
    .switch {
      position: relative;
      display: inline-block;
      width: 60px;
      height: 34px;
      margin: 0 auto -0.75em;
      input[type=checkbox] {
        display: none;
        margin: 4px 0 0;
        margin-top: 1px\9;
        line-height: normal;
        padding: 0;
        &:checked+.slider {
          background-color: #396add;
          &:before {
            transform: translateX(26px);
          }
        }
      }
      .slider {
        position: absolute;
        cursor: pointer;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: #673ab7;
        transition: .4s;
        &.round {
          border-radius: 34px;
          &:before {
            border-radius: 50%;
            position: absolute;
            content: "";
            height: 26px;
            width: 26px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
          }
        }
      }
    }
  }
  </style>