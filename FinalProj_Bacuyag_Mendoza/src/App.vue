<template>
  <div class="cardContainer">
    <div class="cardGrid">
      <!-- List of cards -->
      <div v-for="(card, index) in cards" :key="index" @click="showModal(card)">
        <div class="card">
          <img :src="card.image" alt="Card Image" style="max-width: 100%; max-height: 100%;">
          <p>{{ card.shortText }}</p>
        </div>
      </div>

      <!-- Modal -->
      <div v-if="modalVisible" class="modal" @click="hideModal">
        <div class="modal-content" @click.stop>
          <div class="content-wrapper">
            <img :src="selectedCard.image" alt="Expanded Image" class="expanded-image">
            <div class="video-wrapper">
              <iframe width="560" height="315" :src="selectedCard.videoUrl" frameborder="0" allowfullscreen></iframe>
            </div>
            <p class="short-text">{{ selectedCard.shortText }}</p>
            <p>{{ selectedCard.longText }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cards: [
        {
          image: 'https://i.redd.it/anyone-able-to-clean-up-these-two-screenshots-as-wallpaper-v0-xohr3hh15cxb1.jpg?width=1920&format=pjpg&auto=webp&s=5aae4f51cde364ab7105d9541284279d67e6477e',
          videoUrl: 'https://www.youtube.com/embed/dQw4w9WgXcQ',
          shortText: 'Short text 1',
          longText: 'Long text 1'
        },
        {
          image: 'https://via.placeholder.com/200',
          videoUrl: 'https://www.youtube.com/embed/dQw4w9WgXcQ',
          shortText: 'Short text 2',
          longText: 'Long text 2'
        },
        {
          image: 'https://via.placeholder.com/200',
          videoUrl: 'https://www.youtube.com/embed/dQw4w9WgXcQ',
          shortText: 'Short text 3',
          longText: 'Long text 3'
        },
        {
          image: 'https://via.placeholder.com/200',
          videoUrl: 'https://www.youtube.com/embed/dQw4w9WgXcQ',
          shortText: 'Short text 4',
          longText: 'Long text 4'
        },
        {
          image: 'https://via.placeholder.com/200',
          videoUrl: 'https://www.youtube.com/embed/dQw4w9WgXcQ',
          shortText: 'Short text 5',
          longText: 'Long text 5'
        },
        {
          image: 'https://via.placeholder.com/200',
          videoUrl: 'https://www.youtube.com/embed/dQw4w9WgXcQ',
          shortText: 'Short text 6',
          longText: 'Long text 6'
        }
      ],
      modalVisible: false,
      selectedCard: {}
    };
  },
  methods: {
    showModal(card) {
      this.selectedCard = card;
      this.modalVisible = true;
    },
    hideModal() {
      this.modalVisible = false;
    }
  }
}
</script>
<style scoped>
/* CSS for styling */

.cardContainer {
  display: flex;
  justify-content: center;
}

.cardGrid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  max-width: 900px;
  /* Set a max-width to limit the size of the grid */
}

.card {
  width: 225px;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  cursor: pointer;
  margin-bottom: 20px;
  box-sizing: border-box;
  /* Add margin-bottom to create space between rows */
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
  /* Set text color to black */
}

.short-text {
  font-size: 18px;
  /* Set font size to 18px */
  font-weight: bold;
  /* Set font weight to bold */
}

.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.expanded-image {
  max-width: 60%;
  /* Limit maximum width of the image to 80% of the modal width */
  max-height: 60vh;
  /* Limit maximum height of the image to 80% of the viewport height */
}

.video-wrapper {
  margin-top: 20px;
}
</style>
