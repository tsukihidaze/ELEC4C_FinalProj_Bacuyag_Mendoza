<template>
  <div>
    <div class="card" v-for="(card, index) in cards" :key="index" @click="toggleExpand(index)">
      <img :src="card.image" alt="Card Image">
      <div class="card-content">
        <h2>{{ card.title }}</h2>
        <p v-if="!card.expanded">{{ card.shortText }}</p>
        <div v-if="card.expanded">
          <p>{{ card.fullText }}</p>
          <iframe v-if="card.video" :src="card.video" frameborder="0" allowfullscreen></iframe>
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
          title: "Card 1",
          shortText: "Short text for card 1",
          fullText: "Full text for card 1",
          image: "path_to_image_for_card_1.jpg",
          video: "youtube_video_embed_code_for_card_1",
          expanded: false
        },
        {
          title: "Card 2",
          shortText: "Short text for card 2",
          fullText: "Full text for card 2",
          image: "path_to_image_for_card_2.jpg",
          video: "youtube_video_embed_code_for_card_2",
          expanded: false
        },
        // Add more cards as needed
      ]
    };
  },
  methods: {
    toggleExpand(index) {
      this.cards[index].expanded = !this.cards[index].expanded;
    }
  }
};
</script>

<style scoped>
.card {
  border: 1px solid #ccc;
  margin: 10px;
  padding: 10px;
  cursor: pointer;
}

.card-content {
  margin-top: 10px;
}

.card img {
  width: 100%;
  height: auto;
}
</style>
