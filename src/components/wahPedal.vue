<template>
</template>
<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'wah-pedal',
    methods: {
        changeText (inputs) {
            let result = ''
            let count = 0
            for (let input in inputs) { 
                if (count < this.maxInputs) {
                    for (let i = 0; i < input.length - 1; i++) {
                        switch(i % 3) {
                            case 0:
                                result += 'w'
                                break
                            case 1:
                                result += 'a'
                                break
                            case 2:
                                result += 'h'
                                break
                            case 3:
                                result += 'h'
                                break
                            default:
                                result += 'a'
                                break

                        }
                    }
                    return this.saveOutput(result)
                    count ++
                }
            }
        }
    }
}
</script>

<style>
</style>
