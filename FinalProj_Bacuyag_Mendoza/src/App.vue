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
 
       <!-- Modal for displaying card details -->
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
 
       <!-- Input Modal for adding new cards -->
       <div v-if="inputModalVisible" class="modal" @click="hideInputModal">
         <div class="modal-content" @click.stop>
           <form @submit.prevent="addCard">
             <input type="text" v-model="newCard.image" placeholder="Image URL">
             <input type="text" v-model="newCard.videoUrl" placeholder="Video URL">
             <input type="text" v-model="newCard.shortText" placeholder="Short Text">
             <input type="text" v-model="newCard.longText" placeholder="Long Text">
             <button type="submit">Add Card</button>
           </form>
         </div>
       </div>
     </div>
     <!-- Floating "+" Button -->
     <button class="floatingButton" @click="showInputModal">+</button>
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
         // Add more cards as needed
       ],
       modalVisible: false,
       selectedCard: {},
       inputModalVisible: false,
       newCard: {
         image: '',
         videoUrl: '',
         shortText: '',
         longText: ''
       }
     };
  },
  methods: {
     showModal(card) {
       this.selectedCard = card;
       this.modalVisible = true;
     },
     hideModal() {
       this.modalVisible = false;
     },
     showInputModal() {
       this.inputModalVisible = true;
     },
     hideInputModal() {
       this.inputModalVisible = false;
     },
     addCard() {
       this.cards.push(this.newCard);
       this.newCard = {
         image: '',
         videoUrl: '',
         shortText: '',
         longText: ''
       };
       this.inputModalVisible = false;
     }
  }
 }
 </script>
 
 <style scoped>
 .cardContainer {
  display: flex;
  justify-content: center;
 }
 
 .cardGrid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  max-width: 900px;
  background-color: #f8f9fa;
  padding: 20px;
  border-radius: 8px;
 }
 
 .card {
  width: 225px;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  cursor: pointer;
  margin-bottom: 20px;
  box-sizing: border-box;
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
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
 }
 
 /* Floating Button Styling */
 .floatingButton {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 50px;
  height: 50px;
  background-color: #007bff;
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
 