<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'reverse-pedal',
    methods: {
        changeText (inputs) { // change text must return a single string... for now
            let result = ""

            if (inputs == undefined) {
                console.warn("inputs is undefined")
            } else {
                this.log("these are the inputs")
                this.log(this.inputs)

                for(let singleInput in this.inputs){
                    this.log("outputting a single input")
                    this.log(singleInput)
                    this.log(this.inputs[singleInput])
                }

                result += this.inputs["mainInput"].split('').reverse().join('')
            }

            return result
        }
    }
}
</script>

<style>
</style>
