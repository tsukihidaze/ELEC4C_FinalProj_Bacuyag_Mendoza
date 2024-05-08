<script setup>
import { ref, onMounted, computed } from 'vue'

const query = ref('')
const my_anime = ref([])
const search_results = ref([])

const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title)
  })
})
const topAnime = ref([]);

onMounted(async () => {
  topAnime.value = await fetchTopAnime();
});
const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
    .then(res => res.json())
    .then(data => {
      search_results.value = data.data
    })
}
const removeAnime = (anime) => {
  my_anime.value = my_anime.value.filter(item => item.id !== anime.id);
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value));
}
const handleInput = (e) => {
  if (!e.target.value) {
    search_results.value = []
  }
}
const fetchTopAnime = async () => {
  const url = 'https://api.jikan.moe/v4/top/anime';
  try {
    const response = await fetch(url);
    const data = await response.json();
    return data.data;
  } catch (error) {
    console.error('Error fetching top anime:', error);
    return [];
  }
};
const updateReview = (anime) => {
  anime.review = anime.newReview;
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value));
}

const addAnime = (anime) => {
  search_results.value = []
  query.value = ''

  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0,
  })

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increaseWatch = (anime) => {
  anime.watched_episodes++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = (anime) => {
  anime.watched_episodes--
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})

const cards = ref(JSON.parse(localStorage.getItem('animeOdysseyCards')) || [])
const modalVisible = ref(false)
const selectedCard = ref({})
const inputModalVisible = ref(false)
const newCard = ref({
  image: "",
  videoUrl: "",
  shortText: "",
  longText: "",
})

const isURLValid = computed(() => {
  const urlRegex = /^(ftp|http|https):\/\/[^ "]+$/;
  return urlRegex.test(newCard.value.image) && urlRegex.test(newCard.value.videoUrl);
})


const showModal = (card) => {
  selectedCard.value = card
  modalVisible.value = true
}

const hideModal = () => {
  modalVisible.value = false
}

const showInputModal = () => {
  inputModalVisible.value = true
}

const hideInputModal = () => {
  inputModalVisible.value = false
}

const addCard = () => {
  if (!isURLValid.value) {
    alert('Please enter valid URLs for Image and Video.')
    return
  }

  cards.value.push(newCard.value)
  newCard.value = {
    image: "",
    videoUrl: "",
    shortText: "",
    longText: "",
  }
  localStorage.setItem('animeOdysseyCards', JSON.stringify(cards.value))
  inputModalVisible.value = false
}

const deleteCard = (index) => {
  cards.value.splice(index, 1);
  localStorage.setItem('animeOdysseyCards', JSON.stringify(cards.value));
}


</script>

<template>
  <main class="layout-container">
    <div class="section">
      <h1>Top Anime</h1>
      <div class="top-anime-container">
        <div class="top-anime">
          <div v-for="(anime, index) in topAnime" :key="anime.mal_id" class="anime-card">
            <img :src="anime.images.jpg.image_url" alt="Anime Cover" />
            <div class="anime-details">
              <h3>{{ anime.title }}</h3>
              <p>{{ anime.synopsis.slice(0, 120) }}...</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="section">
      <div class="anime-spaced">
        <div class="myanime" v-if="my_anime.length > 0">
          <h1>My Anime List</h1>

          <div class="myanime-scroll">
            <div v-for="anime in my_anime_asc" class="anime">
              <div class="anime-details anime-list-details">
                <img :src="anime.image" class="anime-list-image" />
                <h3>{{ anime.title }}</h3>
              </div>
              <div class="flex-1"></div>
              <div class="anime-actions">
                <span class="episodes">{{ anime.watched_episodes }} / {{ anime.total_episodes }}</span>
                <button v-if="anime.total_episodes !== anime.watched_episodes" @click="increaseWatch(anime)"
                  class="button">+</button>
                <button v-if="anime.watched_episodes > 0" @click="decreaseWatch(anime)" class="button">-</button>
                <div class="remove-button" @click="removeAnime(anime)">x</div>
              </div>
              <div class="review-container">
                <textarea v-model="anime.newReview" placeholder="Add/Edit Review"></textarea>
                <button @click="updateReview(anime)" class="button">Save Review</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="section">
      <div class="search-container">
        <header class="header">
          <h1>Anime Odyssey</h1>
        </header>
        <h1>Anime Search</h1>
        <form @submit.prevent="searchAnime">
          <input type="text" placeholder="Search" v-model="query" @input="handleInput" />
          <button type="submit" class="button">Search</button>
        </form>

        <div class="results" v-if="search_results.length > 0">
          <div v-for="anime in search_results" class="result">
            <img :src="anime.images.jpg.image_url" />
            <div class="details">
              <h3>{{ anime.title }}</h3>
              <p :title="anime.synopsis" v-if="anime.synopsis">{{ anime.synopsis.slice(0, 120) }}...</p>
              <span class="flex-1"></span>
              <button @click="addAnime(anime)" class="button">Add to My Anime</button>
            </div>
          </div>
        </div>


      </div>
    </div>

    <footer class="footer">
      <p>&copy; 2024 Anime Odyssey. All rights reserved.</p>
    </footer>
  </main>
</template>


<style scoped>
.anime {
  display: flex;
  flex-direction: column;
}

.myanime-scroll {
  max-height: 650px;
  overflow-y: auto;
}

.anime-list-details {
  display: flex;
  justify-content: center;
  align-items: center;
}

.anime-list-image {
  max-width: 100%;
  height: auto;
}

.anime-actions {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}


textarea {
  width: 100%;
  padding: 5px;
  resize: vertical;
  min-height: 50px;
}

.anime-card {
  width: calc(33.33% - 20px);
  margin: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.review-container {
  margin-bottom: 10px;
}

@media screen and (max-width: 1200px) {
  .anime-card {
    width: calc(50% - 20px);

  }
}

@media screen and (max-width: 768px) {
  .anime-card {
    width: calc(100% - 20px);

  }
}

.top-anime-container {
  overflow-y: auto;
  max-height: 650px;
}

.section {
  margin-top: 15px;
  max-width: 36%;
  padding: 20px;
}

.section h1 {
  margin-top: 5px;
  padding-bottom: 20px;
  text-align: center;
}

.cardContainer {
  display: flex;
  justify-content: center;
  color: black;
}

.cardGrid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  max-width: 900px;
  background-color: #f8f9fa;
  padding: 20px;
  border-radius: 8px;
  color: black;
}

.card {
  width: 225px;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  cursor: pointer;
  margin-bottom: 20px;
  box-sizing: border-box;
  color: black;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  color: black;
}

.short-text {
  font-size: 18px;
  font-weight: bold;
}

.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.expanded-image {
  max-width: 60%;
  max-height: 60vh;
}

.video-wrapper {
  margin-top: 20px;
}

.modal form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.modal form input {
  padding: 5px;
  margin-bottom: 10px;
}

.modal form button {
  padding: 5px 10px;
  background-color: #aa00ff;
  color: white;
  border: none;
  cursor: pointer;
}

.layout-container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding: 20px;
}


.cardGrid {
  flex: 1;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(225px, 1fr));
  gap: 20px;
  padding: 20px;
}

.layout-container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 20px;
}

.cardGrid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(225px, 1fr));
  gap: 20px;
  padding: 20px;
  max-width: 900px;
}


.floatingButton {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 50px;
  height: 50px;
  background-color: #aa00ff;
  color: white;
  border: none;
  border-radius: 50%;
  font-size: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.floatingButton:hover {
  background-color: #aa00ff;
}

.header {
  position: fixed;
  top: 0;
  left: 0;
  background-color: #aa00ff;
  color: white;
  text-align: center;
  border-radius: 8px;
  width: 100%;
  padding-bottom: 1%;
}

.footer {
  background-color: #aa00ff;
  color: white;
  text-align: center;
  padding: 10px;
  position: fixed;
  bottom: 0;
  width: 100%;
  left: 0;
}

.floatingButton {
  position: fixed;
  bottom: 80px;
  right: 20px;
  width: 50px;
  height: 50px;
  background-color: #c300ff;
  color: white;
  border: none;
  border-radius: 50%;
  font-size: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.floatingButton:hover {
  background-color: #0056b3;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Fira Sans', sans-serif;
}

body {
  background-color: #EEE;
}

main {
  margin-top: 3%;
  padding: 1.5rem;

}

form {
  display: flex;
  max-width: 480px;
  margin: 0 auto 1.5rem;
}

form input {
  appearance: none;
  outline: none;
  border: none;
  background: white;

  display: block;
  color: #888;
  font-size: 1.125rem;
  padding: 0.5rem 1rem;
  width: 100%;
}

.button {
  padding: 5px 10px;
  background-color: #aa00ff;
  color: white;
  border: none;
  cursor: pointer;
  font-size: 14px;
  font-weight: bold;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: #0056b3;
}

.remove-button {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  padding-left: 15px;
}

.remove-button span {
  font-size: 20px;
  color: red;
}

.results {
  background-color: #fff;
  border-radius: 0.5rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  max-height: 480px;
  overflow-y: scroll;
  margin-bottom: 1.5rem;
}

.result {
  display: flex;
  margin: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  transition: 0.4s;
}

.result img {
  width: 100px;
  border-radius: 1rem;
  margin-right: 1rem;
}

.details {
  flex: 1 1 0%;
  display: flex;
  align-items: flex-start;
  flex-direction: column;
}

.details h3 {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
}

.details p {
  font-size: 0.875rem;
  margin-bottom: 1rem;
}

.details .button {
  margin-left: auto;
}

.flex-1 {
  display: block;
  flex: 1 1 0%;
}

.myanime h1 {
  color: #181818;
  font-weight: 400;
  margin-bottom: 1.5rem;
}

.myanime .anime {
  display: flex;
  align-items: center;
  margin-bottom: 1.5rem;
  background-color: #f1f1f1c2;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 1px 8px 16px rgba(0, 0, 0, 0.1);
}

.anime img {
  width: 72px;
  height: 72px;
  object-fit: cover;
  border-radius: 1rem;
  margin-right: 1rem;
}

.anime h3 {
  color: #888;
  font-size: 1.125rem;
}

.anime .episodes {
  margin-right: 1rem;
  color: #888;
}

.anime .button:first-of-type {
  margin-right: 0.5rem;
}

.anime .button:last-of-type {
  margin-right: 0;
}

.cardContainer {
  display: flex;
  justify-content: center;
  color: black;
}

.cardGrid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  max-width: 900px;
  background-color: #f8f9fa;
  padding: 20px;
  border-radius: 8px;
  color: black;
}

.delete-button {
  background-color: red;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  margin-left: 10px;
}


.card {
  width: 225px;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  cursor: pointer;
  margin-bottom: 20px;
  box-sizing: border-box;
  color: black;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  color: black;
}

.short-text {
  font-size: 18px;
  font-weight: bold;
}

.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.expanded-image {
  max-width: 60%;
  max-height: 60vh;
}

.video-wrapper {
  margin-top: 20px;
}

.modal form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.modal form input {
  padding: 5px;
  margin-bottom: 10px;
}

.modal form button {
  padding: 5px 10px;
  background-color: #aa00ff;
  color: white;
  border: none;
  cursor: pointer;
}

.floatingButton {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 50px;
  height: 50px;
  background-color: #aa00ff;
  color: white;
  border: none;
  border-radius: 50%;
  font-size: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.floatingButton:hover {
  background-color: #aa00ff;
}

.header {
  position: fixed;
  background-color: #aa00ff;
  color: white;
  padding: 20px;
  text-align: center;
  border-radius: 8px;
  width: 100%;
  z-index: 100;
}

.header input {
  padding: 5px;
  margin: 5px;
}

.header button {
  padding: 5px 10px;
  background-color: white;
  color: #aa00ff;
  border: none;
  cursor: pointer;
}

.footer {
  background-color: #aa00ff;
  color: white;
  text-align: center;
  padding: 10px;
  position: fixed;
  bottom: 0;
  width: 100%;
  left: 0;
  z-index: 50;
}

.floatingButton {
  position: fixed;
  bottom: 80px;
  right: 20px;
  width: 50px;
  height: 50px;
  background-color: #c300ff;
  color: white;
  border: none;
  border-radius: 50%;
  font-size: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.floatingButton:hover {
  background-color: #0056b3;
}

.top-anime {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.anime-card {
  width: calc(33.33% - 20px);
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.anime-card img {
  width: 100%;
  height: auto;
  border-radius: 8px;
}

.anime-details {
  margin-top: 10px;
}
</style>
