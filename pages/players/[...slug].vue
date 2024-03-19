<script setup lang="ts">


const {findOne} = useStrapi()
const route = useRoute()
const {data, pending} = useAsyncData('player', 
    () => findOne(`players/${route.params.slug}`).then(res => res.data)
)
</script>

<template>
    <h1>{{data.name}} </h1> 
    <h2>Ranking : {{data.ranking}}</h2>

    <div class="flex">
        <div>
            <img :src="data.image.url" alt="photo de {{data.name}}">
        </div>
        <div>
            <h2>Compétitions</h2>
            <ul>
              <li v-for="competition in data.competitions" :key="competition.id">
                 <p>{{competition.name}}</p>
              </li>
            </ul>
         </div>
    </div>
    <!-- :href="`/competitions/${competition.id}`" -->
    <pre>
    {{ data }}
    </pre>
</template>

<style>
.flex{
    display: flex;
    justify-content: space-around ;
}

img{
    width: 200px;
    height: 200px;
    border-radius: 50%;
    object-fit: cover;
    margin: 20px;
}
</style>