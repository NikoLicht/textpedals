<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'reverb-pedal',
    methods: {
        changeText (inputs) {
            let result = ''

            if (inputs == undefined) {
                console.warn("inputs is undefined")
            } else { // If there is a main Input

                // get individual words
                let seperatedWords = this.inputs["mainInput"].split(" ")

                for (let word of seperatedWords) {

                    let ending = word.slice((word.length/3) * 2, word.length)
                    result += word
                    result += ending
                    result += " "
                }

            }

            return result
        }
    }
}
</script>

<style>
</style>
