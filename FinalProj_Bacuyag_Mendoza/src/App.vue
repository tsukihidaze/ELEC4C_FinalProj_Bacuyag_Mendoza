<script setup>
import { ref, onMounted, computed } from 'vue'
import LoginModal from '/src/components/LoginModal.vue';

const query = ref('')
const my_anime = ref([])
const search_results = ref([])

const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title)
  })
})

const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
 .then(res => res.json())
 .then(data => {
    search_results.value = data.data
  })
}

const handleInput = (e) => {
  if (!e.target.value) {
    search_results.value = []
  }
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

const openLoginModal = () => {
  // Assuming LoginModal has an openModal method
  // This might need to be adjusted based on the actual implementation of LoginModal
}

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
  cards.value.splice(index, 1); // Remove the card from the array
  localStorage.setItem('animeOdysseyCards', JSON.stringify(cards.value)); // Update local storage
}


</script>

<template>
  <main>
	<header class="header">
    <h1>Anime Odyssey</h1>
    <button @click="openLoginModal">Login</button>
  </header>
    <h1>My Anime Tracker</h1>
  
    <form @submit.prevent="searchAnime">
      <input type="text" placeholder="Search for an anime..." v-model="query" @input="handleInput" />
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

    <div class="myanime" v-if="my_anime.length > 0">
      <h2>My Anime</h2>

      <div v-for="anime in my_anime_asc" class="anime">
        <img :src="anime.image" />
        <h3>{{ anime.title }}</h3>
        <div class="flex-1"></div>
        <span class="episodes">{{ anime.watched_episodes }} / {{ anime.total_episodes }}</span>
        <button 
          v-if="anime.total_episodes!== anime.watched_episodes" 
          @click="increaseWatch(anime)" class="button">+</button>
        <button 
          v-if="anime.watched_episodes > 0"
          @click="decreaseWatch(anime)" class="button">-</button>
      </div>
    </div>

	<div class="cardContainer">
    <div class="cardGrid">
      <div v-for="(card, index) in cards" :key="index" @click="showModal(card)">
        <div class="card">
          <img :src="card.image" alt="Card Image" style="max-width: 100%; max-height: 100%" />
          <p>{{ card.shortText }}</p>
          <button class="delete-button" @click="deleteCard(index)">Delete</button>

        </div>
      </div>
      <!-- hide and show card modal fcn -->
      <div v-if="modalVisible" class="modal" @click="hideModal">
        <div class="modal-content" @click.stop>
          <div class="content-wrapper">
            <img :src="selectedCard.image" alt="Expanded Image" class="expanded-image" />
            <div class="video-wrapper">
              <iframe width="560" height="315" :src="selectedCard.videoUrl" frameborder="0" allowfullscreen></iframe>
            </div>
            <p class="short-text">{{ selectedCard.shortText }}</p>
            <p>{{ selectedCard.longText }}</p>
          </div>
        </div>
      </div>
      <!-- hide and show NEW card input modal fcn -->
      <div v-if="inputModalVisible" class="modal" @click="hideInputModal">
        <div class="modal-content" @click.stop>
          <form @submit.prevent="addCard">
            <input type="text" v-model="newCard.image" placeholder="Image URL" />
            <input type="text" v-model="newCard.videoUrl" placeholder="Video URL" />
            <input type="text" v-model="newCard.shortText" placeholder="Short Text" />
            <input type="text" v-model="newCard.longText" placeholder="Long Text" />
            <button type="submit">Add</button>
          </form>
        </div>
      </div>

    </div>

    <!-- add content button (inputModal) -->
    <button class="floatingButton" @click="showInputModal">+</button>

    <footer class="footer">
      <p>&copy; 2024 Anime Odyssey. All rights reserved.</p>
    </footer>
  </div>
  </main>
</template>


<style scoped>

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

.search-container {
  flex: 1;
}

.cardGrid {
  flex: 1;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(225px, 1fr));
  gap: 20px;
  padding: 20px;
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

body {
  margin: 0;
  padding: 0;
}

.header {
  position: fixed;
  top: 0;
  left: 0;
  background-color: #aa00ff;
  color: white;
  padding: 20px;
  text-align: center;
  border-radius: 8px;
  width: 100%;
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
  margin-top: 60px; /* Adjust based on the height of your header */
  padding: 1.5rem;
}


h1 {
	text-align: center;
	margin-bottom: 1.5rem;
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
	appearance: none;
	outline: none;
	border: none;
	background: none;
	cursor: pointer;

	display: block;
	padding: 0.5rem 1rem;
	background-image: linear-gradient(to right, deeppink 50%, darkviolet 50%);
	background-size: 200%;
	color: white;
	font-size: 1.125rem;
	font-weight: bold;
	text-transform: uppercase;
	transition: 0.4s;
}

.button:hover {
	background-position: right;
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

.myanime h2 {
	color: #888;
	font-weight: 400;
	margin-bottom: 1.5rem;
}

.myanime .anime {
	display: flex;
	align-items: center;
	margin-bottom: 1.5rem;
	background-color: #FFF;
	padding: 1rem;
	border-radius: 0.5rem;
	box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
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
  margin-left: 10px; /* Add some space between the delete button and other elements */
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

body {
  margin: 0;
  padding: 0;
}
.header {
  position: fixed;
  top: 0;
  left: 0;
  background-color: #aa00ff;
  color: white;
  padding: 20px;
  text-align: center;
  border-radius: 8px;
  width: 100%;
  z-index: 100; /* Adjust z-index as needed */
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
  z-index: 50; /* Lower z-index than the header */
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
</style>
