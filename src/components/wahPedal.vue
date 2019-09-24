<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'wah-pedal',
    methods: {
        changeText (inputs) {
            let result = ''
            let vocals = ["a", "e", "i", "o", "u", "y"]

            if (inputs == undefined) {
                console.warn("inputs is undefined")
            } else { // If there is a main Input

                // Vocals become wah
                for (let i = 0; i < this.inputs["mainInput"].length - 1; i++) {
                    let currentChar = this.inputs["mainInput"].charAt(i)

                    if ( vocals.includes(currentChar)) {
                        result += "wah"
                    }
                    else {
                        result += currentChar
                    }
                }
            }

            return result
        }
    }
}
</script>

<style>
</style>
