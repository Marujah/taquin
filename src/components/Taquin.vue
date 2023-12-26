<script setup lang="ts">
import Piece from './Piece.vue';
import GridConfig from './GridConfig.vue';
import PictureConfig from './PictureConfig.vue';
import Modal from './Modal.vue';
import { computed, ref, watch} from 'vue';

type TPiece = { number: number, position: string}

const shuffledPieces = ref<TPiece[]>([]);
const moves = ref(0);
const grid = ref(3);
const gridGap = ref('1px');
const bgStyle = ref('none');
const gameStarts = ref(false);
const gameIsDone = ref(false);
const randomMoves = ref<string[]>([]);
const freePosition = ref('');

function getPossibleMoves(position: string, allPos: string[]) {
    let positionArray = [...position]
    let possiblePositions = []
    possiblePositions[0] = (+positionArray[0] - 1) + positionArray[1]
    possiblePositions[1] = (+positionArray[0] + 1) + positionArray[1]
    possiblePositions[2] = positionArray[0] + (+positionArray[1] - 1)
    possiblePositions[3] = positionArray[0] + (+positionArray[1] + 1)
    return possiblePositions.filter((item) => { return allPos.indexOf(item) !== -1 })
}

function getReversedMove(move: string) {
    return move.split('-').reverse().join('-')
}

function chooseRandomMove(arr: string[] ) {
    let j = Math.floor(Math.random() * ((arr.length - 1) + 1))
    if (freePosition.value === arr[j] ||
        (randomMoves.value.length > 0 && getReversedMove(freePosition.value + '-' + arr[j]) === randomMoves.value[randomMoves.value.length - 1]) ||
        randomMoves.value.indexOf(`${freePosition.value}-${arr[j]}`) !== -1 ||
        isGameDone(winningPieces.value, shuffledPieces.value)) {
        j = Math.floor(Math.random() * ((arr.length - 1) + 1))
    }
    randomMoves.value.push(`${freePosition.value}-${arr[j]}`)
    freePosition.value = arr[j]
    return arr[j]
}

function isGameDone(arr1: any[], arr2: any[]) {
    const arr3 = arr2.sort((a, b) => { return a.number > b.number ? 1 : (a.number < b.number ? -1 : 0) })
    if (arr1.length !== arr3.length) return false
    if (JSON.stringify(arr1) !== JSON.stringify(arr3)) return false
    gridGap.value = '0px';
    return true
}

function onPieceMoved(piece: any, newFreePosition: any) {
    console.log(piece, newFreePosition)
    moves.value++
    freePosition.value = newFreePosition
    shuffledPieces.value.map((item) => {
        if (item.number === piece.number) {
            item.position = piece.myPosition
        }
        return item
    })
    // check if game done
    let currentGamePieces = shuffledPieces.value.slice(0);
    currentGamePieces = currentGamePieces.sort((a, b) => a.number - b.number);
    if (isGameDone(winningPieces.value, currentGamePieces)) {
        gameIsDone.value = true;
    }
}
function changePieceBackground(background: string) {
    freePosition.value = allPositions.value[allPositions.value.length - 1]
    shuffledPieces.value = cloneArray(winningPieces.value)
    bgStyle.value = background
    gameStarts.value = false
    randomMoves.value = []
}

function gridChanged(newGrid: number) {
    grid.value = newGrid;
    shuffledPieces.value = [];
    resetGame();
}

function resetGame() {
    moves.value = 0;
    gameStarts.value = false;
    gameIsDone.value = false;
    randomMoves.value = []
    freePosition.value = '';
}

function shuffle(arr: string[]) {
    arr.some((item) => {
        let piecePosition = item.split('-')[1]
        for (let prop of shuffledPieces.value) {
            if (prop.position === piecePosition) {
                prop.position = item.split('-')[0]
                break
            }
        }
    })
    return shuffledPieces.value
}

function startGame() {
    randomMoves.value = []
    let possibleMoves = getPossibleMoves(freePosition.value, allPositions.value)
    let count = 0
    do {
        chooseRandomMove(possibleMoves)
        possibleMoves = getPossibleMoves(freePosition.value, allPositions.value)
        count++
    } while (count < 80 || freePosition.value !== allPositions.value[allPositions.value.length - 1])
    // sort shuffled array over positions
    shuffledPieces.value = shuffle(randomMoves.value).sort(function (a, b) { return (a.position > b.position) ? 1 : ((b.position > a.position) ? -1 : 0) });
    moves.value = 0
    gameStarts.value = true
}

function cloneArray(inputArr: any) {
    return JSON.parse(JSON.stringify(inputArr))
}

const pieceWidth = computed(() => Math.floor(578 / grid.value));
const pieceHeight = computed(() => Math.floor(578 / grid.value));
const allPositions = computed<string[]>(() => {
    let result = []
    for (let i = 0; i < (grid.value * grid.value); i++) {
        let x = Math.floor(i / grid.value)
        let y = i % grid.value
        result.push(x + '' + y)
    }
    return result
})
const winningPieces = computed<TPiece[]>(() => {
    let result = [];
    for (let i = 0; i < (grid.value * grid.value); i++) {
        let x = Math.floor(i / grid.value)
        let y = i % grid.value
        result.push({ 'number': i + 1, 'position': x + '' + y })
    }
    result.pop();
    return result;
});

watch(allPositions, (newVal) => {
    freePosition.value = newVal[newVal.length - 1];
});

watch(winningPieces, (val) => {
    shuffledPieces.value = cloneArray(val);
})


</script>

<template>
    <div class="main">
        <div class="config">
            <GridConfig @gridchanged="gridChanged"></gridConfig>
            <PictureConfig @bgchanged="changePieceBackground"></PictureConfig>
        </div>
        <div class="game-area">
            <Modal v-if="gameIsDone" @reset="resetGame">
                <p><strong>You win</strong><br><strong>{{ moves }}</strong> moves!!!</p>
            </Modal>
            <button class="start-game" @click="startGame" :disabled="shuffledPieces.length === 0">Start Game</button>
            <div class="moves">Moves: {{ moves }}</div>
            <div class="taquin" :style="{
                gridTemplateColumns: 'repeat(' + grid + ', 1fr)',
                gridTemplateRows: 'repeat(' + grid + ', 1fr)',
                gridGap: gridGap
            }">
                <div class="game-starter" v-if="gameStarts !== true"
                    :style="grid === 3 ? { 'backgroundColor': 'rgba(103, 58, 183, 0.3)' } : { 'backgroundColor': 'rgba(57, 106, 221, 0.3)' }">
                </div>
                <Piece
                    v-for="(piece, index) in shuffledPieces"
                    :key="`${grid}-${allPositions[index]}`"
                    :number="piece.number"
                    :position="allPositions[index]"
                    :allPositions="allPositions"
                    :factor="grid"
                    :myHeight="pieceHeight"
                    :myWidth="pieceWidth" 
                    :freePosition="freePosition"
                    :background="bgStyle"
                    :gameStarts="gameStarts"
                    @moved="onPieceMoved" />
            </div>
        </div>
    </div>
</template>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.main {
    height: 100%;
    position: relative;
    display: grid;
    grid-template-columns: 1fr 2fr;

    .config {
        align-self: center;
    }
}

.start-game {
    border: 1px solid #bab1ec;
    padding: 10px 10px;
    font-weight: bold;
    background-color: #396add;
    color: #fff;
    font-size: 1.25em;
    cursor: pointer;
    outline: 0;
    margin: 1em 0;

    &:hover {
        background-color: darken(#396add, 10%);
    }

    &:disabled {
        cursor: not-allowed;
        background-color: #a9a9aa;
    }
}

.moves {
    text-align: center;
    margin: 1em 0;
    font-weight: 700;
}

.taquin {
    flex: 1 1 auto;
    width: 578px;
    height: 578px;
    display: grid;
    border: 1px solid #000;
    margin: 0 auto;
    padding: 10px;
    position: relative;

    .game-starter {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        z-index: 1;
        cursor: not-allowed;
    }
}
</style>