<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'wordmix-pedal',
    methods: {
        changeText (inputs) {
            let result = ''
            let longestInput = ''
            let shortestInput = ''

            if (inputs == undefined) {
                console.warn("inputs is undefined")
            } 
            else { // If there is inputs

                // Find which input is the shortest and longest
                if (this.inputs["Input A"].length >= this.inputs["Input B"]) {
                    longestInput = this.inputs["Input A"]
                    shortestInput = this.inputs["Input B"]
                }
                else {
                    longestInput =  this.inputs["Input B"]
                    shortestInput = this.inputs["Input A"]
                }
                
                // Take one char from each
                for (let letter = 0; letter < shortestInput.length; letter++){
                    result += shortestInput.charAt(letter) + longestInput.charAt(letter)
                }

                // Append whichever letters are left from the longest word
                result += longestInput.slice(shortestInput.length, longestInput.length)
            }

            return result
        }
    }
}
</script>

<style>
</style>
