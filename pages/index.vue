<script setup lang="ts">
const {find} = useStrapi()

const data = await find('players')
</script>

<template>
    <div>
        <h1>Hello world</h1>    
        <p>lalalala</p>
        <pre v-if="data">{{ data }}</pre>
    </div>
</template>

<style scoped>

</style>