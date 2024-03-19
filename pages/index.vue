<template>
    <div>
        <div class="my-4">
            <h1>Joueurs</h1>
            <label for="choix-couleur">Filtrer les joueurs: </label>
            <select id="choix-couleur" v-model="selectedTag">
                <option value="all">Tous</option>
                <option value="happy">Heureux</option>
                <option value="sad">Triste</option>
                <option value="dead">Mort</option>
                <option v-for="tag in tags" :key="tag.id" :value="tag.name">{{ tag.name }}</option>
            </select>

            <ul>
                <li v-for="player in filteredPlayers" :key="player.slug">
                    <a :href="`/players/${player.slug}`">{{ player.name }}</a>
                </li>
            </ul>
        </div>
    </div>
</template>

<script setup lang="ts">

const { find } = useStrapi();

const selectedTag = ref('all');
const data = ref([]);
const tags = ref([]);

const loadData = async () => {
    try {
        const response = await find('players', { populate: '*' });
        data.value = response.data;
    } catch (error) {
        console.error('Erreur lors du chargement des données des joueurs :', error);
    }
};

const loadTags = async () => {
    try {
        const response = await find('tags');
        tags.value = response.data;
    } catch (error) {
        console.error('Erreur lors du chargement des tags :', error);
    }
};

loadData();
loadTags();

const filteredPlayers = ref([]);

const filterPlayers = () => {
    if (selectedTag.value === 'all') {
        filteredPlayers.value = data.value;
    } else {
        filteredPlayers.value = data.value.filter(player => player.tag === selectedTag.value);
    }
};

watchEffect(() => {
    filterPlayers();
});
</script>

<style scoped>
/* Vos styles CSS ici */
</style> 
