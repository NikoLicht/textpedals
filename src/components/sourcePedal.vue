<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'source-pedal',
    methods: {
        changeText (inputs) { // change text must return a single string... for now
            return "Well you could just not register you as unemployed to the job center"
        }
    }
}
</script>

<style>
</style>
