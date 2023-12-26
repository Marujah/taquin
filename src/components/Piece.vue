<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';

const thisPiece = ref<HTMLDivElement>();
const emit = defineEmits(['moved']);
const props = defineProps(
    [
        'background',
        'number',
        'position',
        'allPositions',
        'freePosition',
        'myWidth',
        'myHeight',
        'factor',
        'gameStarts'
    ]);

const myPosition = ref(props.position);
const myPosX = computed(() => (-props.myWidth * ((props.number - 1) % props.factor)));
const myPosY = computed(() => -(Math.floor((props.number - 1) / props.factor) * props.myHeight));


function getPossiblePositions() {
    let positionArray = [...myPosition.value]
    let possiblePositions = []
    possiblePositions[0] = (+positionArray[0] - 1) + positionArray[1]
    possiblePositions[1] = (+positionArray[0] + 1) + positionArray[1]
    possiblePositions[2] = positionArray[0] + (+positionArray[1] - 1)
    possiblePositions[3] = positionArray[0] + (+positionArray[1] + 1)
    return possiblePositions.filter((item) => { return props.allPositions.indexOf(item) !== -1 })
}
function movePiece() {
    if (props.gameStarts) {
        let possiblePositions = getPossiblePositions()
        let currentPosition = myPosition.value
        if (possiblePositions.indexOf(props.freePosition) !== -1) {
            let moveX = +([...props.freePosition][1]) - +([...currentPosition][1])
            let moveY = +([...props.freePosition][0]) - +([...currentPosition][0])
            let currentPosX = parseInt(window.getComputedStyle(thisPiece.value!).getPropertyValue('--moveX'), 10)
            let currentPosY = parseInt(window.getComputedStyle(thisPiece.value!).getPropertyValue('--moveY'), 10)
            if (thisPiece.value) {
                thisPiece.value.style.setProperty('--moveX', currentPosX + (moveX * (props.myWidth + 1)) + 'px')
                thisPiece.value.style.setProperty('--moveY', currentPosY + (moveY * (props.myHeight + 1)) + 'px')
            }
            myPosition.value = props.freePosition
            emit('moved', {number: props.number, myPosition: myPosition.value}, currentPosition)
        }
    }
}

onMounted(() => {
    thisPiece.value!.style.setProperty('--moveX', '0px')
    thisPiece.value!.style.setProperty('--moveY', '0px')

})
</script>

<template>
    <div class="piece" ref="thisPiece"
        :style="background !== 'none' ? { 'background': 'url(' + background + ') no-repeat ' + myPosX + 'px ' + myPosY + 'px rgba(0, 0, 0, 0.1)' } : { 'background': 'rgba(0, 0, 0, 0.4)' }"
        @click="movePiece" @touchstart="movePiece">
        <span v-if="background === 'none'">{{ number }}</span>
    </div>
</template>

<style lang="less" scoped>
.piece {
    --moveX: 0;
    --moveY: 0;
    background-color: orange;
    border: 1px solid #c7c7c7;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 3em;
    transform: translate3d(var(--moveX), var(--moveY), 0);
    transition: all 0.2s ease-in-out 0s;
    cursor: pointer;

    pre {
        display: block;
        font-size: 10px;
    }
}
</style>