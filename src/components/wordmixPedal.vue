<template>
</template>

<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
    name: 'wordmix-pedal',
    methods: {
        changeText (inputs) {
            let progression = 0
            let result = ''
            let listOfStrings = [] 
            let numberOfInputs = Object.keys(inputs).length

            Object.keys(inputs).forEach((input) => {
                listOfStrings.push(input)
            })

            console.log(listOfStrings)

            listOfStrings.sort((a, b) => {
                return b.length - a.length
            })

            for (let i = 0; i < (listOfStrings[0].length - 1); i ++) {
                for (let j = 0; j < (listOfStrings.length - 1); j++) {
                    if (!this.isEmptyOrNullOrUndefined(listOfStrings[i])) {
                        if (!this.isEmptyOrNullOrUndefined(listOfStrings[i][j])) {
                            result += listOfStrings[i][j]
                        }
                    }
                }
            }


            return this.saveOutput(result)
        }
    }
}
</script>

<style>
</style>
