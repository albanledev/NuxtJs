<template>
    <div>
      <div class="my-4">
        <h1>Joueurs</h1>
        
        <div class="filtre">
            <div>
                <label for="choix-couleur">Filtrer les joueurs: </label>
                <select id="choix-couleur" v-model="selectedTag" @change="filterPlayers">
                  <option value="all">Tous</option>
                  <option value="happy">Heureux</option>
                  <option value="sad">Triste</option>
                  <option value="dead">Mort</option>
                  <option v-for="tag in tags" :key="tag.id" :value="tag.name">{{ tag.name }}</option>
                </select>
            </div>

            <div>
                <label for="search">Recherche par nom: </label>
                <input type="text" id="search" v-model="searchTerm" @input="searchPlayers" />
            </div>
        </div>

        <div v-if="loading" class="loading-message">Chargement en cours...</div>
  
        <ul v-if="!loading">
          <li v-for="player in paginatedPlayers" :key="player.slug">
            <a :href="`/players/${player.slug}`"><img :src="player.image.formats.small.url" :alt="player.image.name" /></a>
            <p>{{ player.name }}</p>
          </li>
        </ul>
  
        <div class="pagination" v-if="!loading">
          <button @click="goToPage(1)" :disabled="currentPage === 1">Début</button>
          <button @click="previousPage" :disabled="currentPage === 1">Précédent</button>
          <span v-for="page in visiblePages" :key="page" @click="goToPage(page)" :class="{ active: currentPage === page }">{{ page }}</span>
          <button @click="nextPage" :disabled="currentPage === pageCount">Suivant</button>
          <button @click="goToPage(pageCount)" :disabled="currentPage === pageCount">Fin</button>
        </div>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  const { find } = useStrapi();
  
  const selectedTag = ref('all');
  const data = ref([]);
  const tags = ref([]);
  const pageSize = 2;
  const currentPage = ref(1);
  const paginatedPlayers = ref([]);
  const visiblePages = ref([]);
  const searchTerm = ref('');
  const loading = ref(true); // Variable de chargement
  
  const loadData = async () => {
    try {
      const response = await find('players', { populate: '*' });
      data.value = response.data;
      loading.value = false; // Mettre à false lorsque le chargement est terminé
    } catch (error) {
      console.error('Erreur lors du chargement des données des joueurs :', error);
      loading.value = false; // Mettre à false en cas d'erreur aussi
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
  
  const filterPlayers = () => {
    let filteredData = data.value;
    if (selectedTag.value !== 'all') {
      filteredData = filteredData.filter(player => player.tag === selectedTag.value);
    }
    if (searchTerm.value.trim() !== '') {
      filteredData = filteredData.filter(player => player.name.toLowerCase().includes(searchTerm.value.toLowerCase()));
    }
    return filteredData;
  };
  
  const pageCount = ref(1);
  
  const updatePagination = () => {
    const filteredData = filterPlayers();
    pageCount.value = Math.ceil(filteredData.length / pageSize);
    paginatedPlayers.value = filteredData.slice((currentPage.value - 1) * pageSize, currentPage.value * pageSize);
    calculateVisiblePages();
  };
  
  const previousPage = () => {
    if (currentPage.value > 1) {
      currentPage.value--;
      updatePagination();
    }
  };
  
  const nextPage = () => {
    if (currentPage.value < pageCount.value) {
      currentPage.value++;
      updatePagination();
    }
  };
  
  const goToPage = (page) => {
    currentPage.value = page;
    updatePagination();
  };
  
  const calculateVisiblePages = () => {
    const totalVisiblePages = 5;
    let startPage = Math.max(1, currentPage.value - Math.floor(totalVisiblePages / 2));
    let endPage = Math.min(pageCount.value, startPage + totalVisiblePages - 1);
  
    while (endPage - startPage + 1 < totalVisiblePages && endPage < pageCount.value) {
      endPage++;
      if (startPage > 1) {
        startPage--;
      }
    }
  
    visiblePages.value = [];
    for (let i = startPage; i <= endPage; i++) {
      visiblePages.value.push(i);
    }
  };
  
  const searchPlayers = () => {
    currentPage.value = 1; 
    updatePagination();
  };
  
  watchEffect(() => {
    updatePagination();
  });
  
  loadData();
  loadTags();
  </script>
  
  <style scoped>
  
    h1 {
      text-align: center;
      font-size: 5rem;
    }
    
    .filtre {
      display: flex;
      justify-content: space-evenly;
      align-items: center;
      margin-bottom: 20px;
      background-color: #f1f1f1;
    padding: 10px;
  }

  select {
    padding: 5px;
    margin-left: 10px;
  }

  ul {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    gap: 20px;
  }

  ul p {
    text-align: center;
  }

  img {
    width: 200px;
    height: 200px;
    object-fit: cover;
  }

  .pagination {
    margin-top: 10px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .pagination button,
  .pagination span {
    margin: 0 5px;
    padding: 5px 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    cursor: pointer;
  }

  .pagination button:disabled,
  .pagination span.disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .pagination span.active {
    background-color: #007bff;
    color: #fff;
  }

  .recherche {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 20px;
    background-color: #f1f1f1;
    padding: 10px;
  }

  .recherche label {
    margin-right: 10px;
  }

  .loading-message {
    text-align: center;
    margin-top: 20px;
  }
  </style>
