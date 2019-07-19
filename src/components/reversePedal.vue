<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'reverse-pedal',
    methods: {
        changeText (inputs) {
            let result = ''
            Object.keys(inputs).forEach((input) => {
                if (this.inputsWentThrough < this.maxInputs) {
                console.log('running')
                    result = input.split('').reverse().join('')
                    this.inputsWentThrough++
                    return this.saveOutput(result)
                }
            })
        }
    }
}
</script>

<style>
</style>
