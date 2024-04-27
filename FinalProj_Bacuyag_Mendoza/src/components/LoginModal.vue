<template>
    <div class="modal" v-if="isOpen" @click.self="closeModal">
       <div class="modal-content" @click.stop>
         <input type="text" v-model="username" placeholder="Username" />
         <input type="password" v-model="password" placeholder="Password" />
         <button @click="login">Login</button>
       </div>
    </div>
   </template>
   
   <script>
   export default {
    data() {
       return {
         isOpen: false,
         username: '',
         password: '',
       };
    },
    methods: {
       openModal() {
         this.isOpen = true;
       },
       closeModal() {
         this.isOpen = false;
       },
       login() {
         // Implement login logic here
         this.closeModal();
       },
    },
   };
   </script>
   