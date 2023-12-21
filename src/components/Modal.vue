<script setup lang="ts">
import { ref } from 'vue';

const backdropRef = ref<HTMLDivElement>()
const emit = defineEmits(['reset'])
function resetGame() {
    emit('reset');
}
</script>

<template>
    <div class="backdrop" ref="backdropRef">
        <div class="modal">
            <a class="close" href="#" @click="resetGame">×</a>
            <slot></slot>
            <button class="reset-btn" @click="resetGame">Ok</button>
        </div>
    </div>
</template>
  
<style scoped lang="less">
.backdrop {
    position: fixed;
    font-size: 70px;
    color: green;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    display: flex;
    opacity: 1;
    justify-content: center;
    align-items: center;
    background-color: rgba(0, 0, 0, 0.8);
    transition: all 0.25s ease-in-out 0s;
    z-index: 10;
    display: flex;
    align-items: center;
    justify-content: center;

    .modal {
        position: relative;
        width: 400px;
        height: 180px;
        background-color: #FFFFFF;
        max-width: 90%;
        max-height: 80%;
        border-radius: 4px;
        border: 2px solid #000000;
        padding: 1em;
        overflow: hidden;
        font-size: 16px;

        p {
            line-height: 1.8;
        }

        .close {
            position: absolute;
            display: inline-block;
            width: 22px;
            height: 24px;
            border-radius: 50%;
            right: 10px;
            top: 10px;
            text-align: center;
            transition: background-color 0.25s ease-out 0s;
            font-size: 24px;
            text-decoration: none;
            line-height: 1;

            &:hover {
                background-color: #c3c3c3;
            }
        }

        .reset-btn {
            border: 1px solid green;
            padding: 5px 10px;
            font-weight: bold;
            background-color: #38863a;
            color: #fff;
            font-size: 1.25em;
            cursor: pointer;
            outline: 0;
            margin-top: 0.5em;

            &:hover {
                background-color: darken(#38863a, 10%);
            }
        }
    }
}</style>