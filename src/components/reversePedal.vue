<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'reverse-pedal',
    methods: {
        changeText (inputs) { // change text must return a single string... for now
            let result = ''
            let inputsWentThrough = 0
            
            if (inputs == undefined) {
                console.warn("inputs is undefined")
            }

            if (!this.isExistant(inputs)) {
                Object.keys(inputs).forEach((input) => {
                    if (inputsWentThrough <= this.maxInputs) {
                        result += inputs[input].split('').reverse().join('')
                    }
                    inputsWentThrough++
                })

                return result
            } else {
                console.warn("The input is non-existant")
                return ""
            }
        }
    }
}
</script>

<style>
</style>
