<template>
  <div class="modal-overlay" @click="handleModalClose">
    <form
      class="add-new-user-modal"
      @click.stop
      @submit="handleAddNewUserFormSubmit"
    >
      <div
        class="add-new-user-modal__close"
        @click="$emit('handleModalClose')"
      />
      <span class="add-new-user-modal__header"
        >Укажите данные нового пользователя</span
      >
      <label class="add-new-user-modal__input-wrapper">
        <input @input="handleUserNameInput" type="text" placeholder="Имя" />
      </label>
      <label class="add-new-user-modal__input-wrapper">
        <input @input="handleUserPhoneInput" type="tel" placeholder="Телефон" />
      </label>

      <select @change="handleUserHeadChange" class="add-new-user-modal__select">
        <option value="" disabled selected>Выберите начальника</option>
        <option v-for="user in userList" :value="user.id" :key="user.name">{{
          user.name
        }}</option>
      </select>

      <button class="add-new-user-modal__submit-button">
        <span>Добавить пользователя</span>
      </button>
    </form>
  </div>
</template>

<script>
import { v4 as uuidv4 } from 'uuid'

export default {
  props: {
    userList: {
      type: Array
    }
  },

  data () {
    return {
      userName: '',
      userPhone: '',
      parentOfUser: ''
    }
  },

  methods: {
    handleAddNewUserFormSubmit (event) {
      event.preventDefault()
      if (!this.userName || !this.userPhone) {
        alert('Имя и телефон - обязательные поля')

        return
      }
      this.$emit('addNewUser', {
        name: this.userName,
        phone: this.userPhone,
        parentOfUser: this.parentOfUser,
        id: uuidv4()
      })
    },

    handleUserNameInput (event) {
      this.userName = event.target.value
    },

    handleUserHeadChange (event) {
      this.parentOfUser = event.target.value
    },

    handleUserPhoneInput (event) {
      this.userPhone = event.target.value
    },

    handleModalClose () {
      this.$emit('handleModalClose')
    }
  }
}
</script>

<style>
.add-new-user-modal__submit-button {
  border: none;
  width: 100%;
  font-size: 20px;
  color: #ffffff;
  background: #3083dc;
  border-radius: 10px;
  padding: 10px 0;
}

.add-new-user-modal__submit-button:hover {
  cursor: pointer;
  background: rgb(20, 87, 159);
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  background: rgba(0, 0, 0, 0.3);
}

.add-new-user-modal__header {
  margin-bottom: 15px;
  font-size: 24px;
}

.add-new-user-modal {
  max-width: 400px;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 200px auto;
  padding: 20px;
  border-radius: 10px;
  background: #ffffff;
}

.add-new-user-modal__input-wrapper {
  width: 100%;
  margin-bottom: 15px;
}

.add-new-user-modal__input-wrapper input {
  width: 100%;
  padding: 5px;
  box-sizing: border-box;
  border: 1px solid #d3d3d3;
  border-radius: 5px;
  font-size: 20px;
}

.add-new-user-modal__input-wrapper input:hover {
  outline: none;
  border: 1px solid #8d8d8d;
  cursor: pointer;
}

.add-new-user-modal__input-wrapper input:focus-visible {
  outline: none;
}

.add-new-user-modal__select {
  width: 100%;
  margin-bottom: 15px;
  padding: 5px;
  font-size: 20px;
  background: none;
  border: 1px solid #d3d3d3;
  border-radius: 5px;
}

.add-new-user-modal__close {
  font-size: 30px;
  position: absolute;
  right: 15px;
  top: 0;
  position: absolute;
}

.add-new-user-modal__close:hover {
  cursor: pointer;
  transform: rotate(90deg);
  transition: 0.5s;
}

.add-new-user-modal__close:after {
  display: inline-block;
  content: "\00d7";
}
</style>
