<template>
    <div id="pedal-base">
        <h1> {{ title }} </h1>
        <div class="input-box">
            <h1 class="input-label">INPUT</h1>
            <div id="inputTexts" v-for="input in inputs" v-bind:key="input">
                <h2 class="input"> {{ input }} </h2>
            </div>
        </div>

        <div class="output-box">
            <h1 class="output-label">OUTPUT</h1>
            <h2 id="output"> {{ output }} </h2>
        </div>
    </div>

</template>

<script>
import { EventBus } from './event-bus.js'

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
            output: ''
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
        }
    },
    mounted: function () {
        this.output = this.changeText(this.inputs)
        this.$emit('new-text', this.output)
        return this.output
    },
    methods: {
        sendOutPut: function (newOutput){
            EventBus.$emit('calculated-new-output', newOutput)
        }
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
