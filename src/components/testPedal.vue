<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'test-pedal',
    methods: {
        changeText (inputs) {
            let result = ''
            console.log('this is the inputs' + inputs)
            console.log(inputs)
            for (let port in inputs) {
                console.log(inputs[port])
                    result += inputs[port] + ' ' 
                }
            console.log(result)
            return this.saveOutput(result)
        }
    }
}
</script>

<style>
</style>
