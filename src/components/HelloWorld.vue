<template>
    <div>
        <h1> {{ title }} </h1>
    </div>

</template>

<script>
export default {
    name: 'pedal-base',
    props: {
        title: String,
        color: String
    }
}
</script>

<style scoped lang="scss">
div{
    background-color: "red"
}

</style>
