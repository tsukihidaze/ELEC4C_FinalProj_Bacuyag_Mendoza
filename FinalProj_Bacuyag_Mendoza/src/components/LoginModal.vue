<template>
    <div class="modal" v-if="isOpen" @click.self="closeModal">
      <div class="modal-content" @click.stop>

        <div class="login-fields">
          <input type="text" v-model="username" placeholder="Username" />
          <input type="password" v-model="password" placeholder="Password" />
        </div>
        <div class="login-button">
          <button @click="login">Login</button>
        </div>
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


<style>
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
  padding: 20px 20px 10px;
  border-radius: 8px;
  color: black;
}


.login-fields {
  display: flex;
  flex-direction: row;
  gap: 20px;
  margin-bottom: 10px; /* Add space below the fields */
}

.login-button {
  display: flex;
  flex-direction: column;
}
.modal-content button {
  padding: 5px 10px;
  background-color: #e188ff;
  color: white;
  cursor: pointer;
  /* Removed margin-left: auto; */
  align-self: center; /* Added for vertical centering */
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
</style>
