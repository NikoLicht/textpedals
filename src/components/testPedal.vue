<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'test-pedal',
    methods: {
        changeText (inputs) {
            let result = ''
            let inputsUsed = 0
            for (let port in inputs) {
                    console.log(inputs[port])
                    inputsUsed++
                    if (inputsUsed <= this.maxInputs) {
                        result += inputs[port] + ' '
                        console.log("port is " + port)
                        console.log("input [port] is " + inputs[port])
                    }
                }
            console.log(`testpedal calculated the result to be ${result}`)
            return result
        }
    }
}
</script>

<style>
</style>
