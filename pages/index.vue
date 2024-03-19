 <script setup lang="ts">
const {find} = useStrapi()
// import type {Player} from '~/models/player.model';

// const data = await find('players')

const { data, pending, error } = await useAsyncData('players', async () => { 
        return await find('players', {
            populate: '*',
        }).then(res => res.data)
    })



    

</script>

<template>
    <div>
        <h1>Hello world</h1>    
        <pre v-if="data">{{ data }}</pre>
        lien cliquable avec slug
        {{ data}}
    


        <template v-if="pending">
            <p>Loading...</p>
        </template>
        <template v-else>
            <div class="my-4">
                <!-- <div v-for="player in players?.data" :key="player.slug">
                    <a :href="`/players/${player.slug}`">{{ player.name }}</a>
                </div> -->
                <a :href="`/players/${player.slug}`" v-for="player in data" :key="player.slug">{{ player.name }}</a>
            </div>
        </template>

    </div>
</template>

<style scoped>

</style> 


