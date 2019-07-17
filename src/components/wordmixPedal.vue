

<script>
import PedalBase from './guitarPedalBase.vue'
export default {
    extends: PedalBase,
   name: 'wordmix-pedal',
   methods: {
        changeText(inputText) {
            let result = ''
            if (true) {
                let primaryString = ''
                let secondaryString = ''
                if (inputText[0].length >= inputText[1].length) {
                    primaryString = inputText[0]
                    secondaryString = inputText[1]
}
                else {
                    primaryString = inputText[1]
                    secondaryString = inputText[0]
}

                for( var i = 0; i <= (primaryString.length - 1); i++) {
                    result += primaryString[i]
                    if (secondaryString[i] != undefined){
                        result += secondaryString[i]
}
}
}

            this.output = result
            return this.output
}
}
}
</script>

<style>
</style>
