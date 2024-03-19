<script setup lang="ts">


const {findOne} = useStrapi()
const route = useRoute()
const {data, pending} = useAsyncData('player', 
    () => findOne(`players/${route.params.slug}`)
)
</script>

<template>
    <h1>Youpi</h1>
</template>