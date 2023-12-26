<script setup lang="ts">
import { ref } from 'vue';

const picture = ref('none');
const pictureConfig = [
    { index: 'pic0', url: 'none' },
    { index: 'pic1', url: './pic1.jpg' },
    { index: 'pic2', url: './pic2.jpg' },
    { index: 'pic3', url: './pic3.jpg' },
    { index: 'pic4', url: './pic4.jpg' },
    { index: 'pic5', url: './pic5.jpg' },
    { index: 'pic6', url: './pic6.jpg' },
    { index: 'pic7', url: './pic7.jpg' },
    { index: 'pic8', url: './pic8.jpg' }
];
const emit = defineEmits(['bgchanged'])
function onPictureChange() {
    emit('bgchanged', picture.value)
}
</script>

<template>
    <div class="image-config">
        <div :key="index" v-for="(pic, index) in pictureConfig">
            <input name="gallery" type="radio" :id="pic.index" :value="pic.url" v-model="picture" @change="onPictureChange"
                :checked="false">
            <label v-if="pic.url === 'none'" :for="pic.index" class="numbers">
                12345...
            </label>
            <label v-else :for="pic.index">
                <img class="config-image" alt="pic1" :src="pic.url">
            </label>
        </div>
    </div>
</template>
  
<style lang="less" scoped>

.image-config {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 8px;
    padding: 5px;

    >div {
        display: inline-block;
        margin: 0 5px;

        label.numbers {
            display: flex;
            justify-content: center;
            align-items: center;
            border: 1px solid #e3e3e3;
            background-color: #fff;
            color: #000;
        }
    }

    input[type=radio] {
        display: none;

        &:checked+label {
            border-color: #000;
            box-shadow: gold 0 0 15px 2px;
        }
    }

    label {
        display: inline-block;
        cursor: pointer;
        width: 100px;
        height: 100px;
        border-radius: 5px;
        transition: all 0.25s ease-in-out 0s;

        &:hover {
            border-color: #000;
        }

        .config-image {
            width: 100px;
            border-radius: 3px;
        }
    }

}</style>