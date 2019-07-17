<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'reverse-pedal',
    methods: {
        changeText (inputText) {
            let result = ''
            console.log(inputText[0])
            result = inputText[0].split('').reverse().join('')
            this.output = result
            return this.output
        }
    }
}
</script>

<style>
</style>
