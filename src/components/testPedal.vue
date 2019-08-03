<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'test-pedal',
    methods: {
        changeText (inputs) {
            let result = ''
            console.log(inputs)
            for (let port in inputs) {
                console.log(inputs[port])
                    result += inputs[port] + ' ' 
                }
            return this.saveOutput(result)
        }
    }
}
</script>

<style>
</style>
