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
    { index: 'pic7', url: './pic7.jpg' }
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
            <label v-if="pic.url !== 'none'" :for="pic.index">
                <img class="config-image" alt="pic1" :src="pic.url">
            </label>
        </div>
    </div>
</template>
  
<style lang="less" scoped>
.grid-config {
    padding: 5px;
    margin-bottom: 2em;
    border-radius: 4px;
    background-color: rgba(0, 0, 0, 0.8);

    [type="radio"]:checked,
    [type="radio"]:not(:checked) {
        position: absolute;
        left: -9999px;
    }

    [type="radio"]:checked+label,
    [type="radio"]:not(:checked)+label {
        position: relative;
        padding-left: 28px;
        cursor: pointer;
        line-height: 20px;
        display: inline-block;
        color: #fff;
    }

    [type="radio"]:checked+label:before,
    [type="radio"]:not(:checked)+label:before {
        content: '';
        position: absolute;
        left: 0;
        top: 0;
        width: 18px;
        height: 18px;
        border: 1px solid #ddd;
        border-radius: 100%;
        background: #fff;
    }

    [type="radio"]:checked+label:after,
    [type="radio"]:not(:checked)+label:after {
        content: '';
        width: 12px;
        height: 12px;
        background: #3b84e8;
        position: absolute;
        top: 3px;
        left: 3px;
        border-radius: 100%;
        -webkit-transition: all 0.2s ease;
        transition: all 0.2s ease;
    }

    [type="radio"]:not(:checked)+label:after {
        opacity: 0;
        -webkit-transform: scale(0);
        transform: scale(0);
    }

    [type="radio"]:checked+label:after {
        opacity: 1;
        -webkit-transform: scale(1);
        transform: scale(1);
    }
}

.image-config {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
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
        }
    }

    input[type=radio] {
        display: none;

        &:checked+label {
            border-color: #000;
            box-shadow: 1px 2px 10px #000;
        }
    }

    label {
        display: inline-block;
        cursor: pointer;
        width: 104px;
        height: 104px;
        border: 2px solid transparent;
        border-radius: 5px;
        transition: all 0.25s ease-in-out 0s;

        &:hover {
            border-color: #000;
            box-shadow: 1px 2px 10px #000;
        }

        .config-image {
            width: 100px;
            border-radius: 3px;
        }
    }

}</style>