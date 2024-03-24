<script setup lang="ts">
const { findOne } = useStrapi();
const route = useRoute();
const { data, pending } = useAsyncData('player', () => {
    return findOne(`players/${route.params.slug}`).then(res => res.data);
});
</script>

<template>
    <div class="flex" v-if="!pending && data">
        <img :src="data.image.url" :alt="data.name">
        <h1>{{ data.name }}</h1>
        <p>Ranking : {{ data.ranking }} <span> | tag : {{ data.tag }}</span></p>
        <div>
            <h2>Compétitions</h2>
            <ul>
                <li v-for="competition in data.competitions" :key="competition.id">
                    <p>{{ competition.name }}</p>
                </li>
            </ul>
        </div>
    </div>

    <div v-else>
        <!-- Indicateur de chargement -->
        <p>Loading...</p>
    </div>
</template>

<style scoped>
.flex {
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
    gap: 0;
}

h1 {
    font-size: 5rem;
    margin: 20px;
}

h2 {
    font-size: 3rem;
    margin: 20px;
}

li {
    padding: 10px;
    border: 1px solid #ccc;
}

img {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    object-fit: cover;
    margin: 20px;
}
</style>
