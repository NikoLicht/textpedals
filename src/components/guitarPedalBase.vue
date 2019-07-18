<template>
    <div id="pedal-base">
        <h1> {{ title }} </h1>
        <div class="input-box" v-on:mouseup="isChild">
            <h1 class="input-label">INPUT</h1>
            <div id="inputTexts" v-for="input in inputs" v-bind:key="input">
                <h2 class="input"> {{ input }} </h2>
            </div>
        </div>

        <div class="output-box" v-on:mousedown="isParent">
            <h1 class="output-label">OUTPUT</h1>
            <h2 id="output"> {{ output }} </h2>
        </div>
    </div>

</template>

<script>
import { bus } from '../main.js'

export default {
    name: 'PedalBase',
    props: {
        title: String,
        color: String,
        inputs: Array,
        chainParent: Object,
        chainChild: Array
    },
    data: function () {
        return {
            output: '',
            id: ''
        }
    },
    watch: {
        inputs: function (val) {
            this.output = this.changeText(this.inputs)
            this.$emit('new-text', this.output)
            return this.output
        }
    },
    methods: {
        changeText: function (inputText) {
            return inputText
        },
        sendOutPut: function (newOutput) {
            bus.$emit('calculated-new-output', newOutput)
        },
        isParent: function () {
            bus.$emit('is-parent', this.id)
        },
        isChild: function () {
            bus.$emit('is-child', this.id)
        },
        calculateId: function () {
            let newId = ''
            function s4 () {
                return Math.floor((1 + Math.random()) * 0x10000)
                    .toString(16)
                    .substring(1)
            }
            newId = s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4()
            this.id = newId
        }
    },
    mounted () {
        this.output = this.changeText(this.inputs)
        this.calculateId()
        return this.output
    }
}

</script>

<style scoped lang="scss">
#pedal-base {
    min-height: 335px;
    width: 250px;
    padding-bottom: 1em;
    background-color: firebrick;
    border-radius: 25px;
    position: absolute;

    .input-box, .output-box{
        border-radius: 25px;
        background-color: lightPink;
        width: 80%;
        margin-left: auto;
        margin-right: auto;
    }

    h1{
        padding-top: 1em;
        font-size: 1em;
        color: white;
    }
}

</style>
