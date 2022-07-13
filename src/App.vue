<template>
  <div id="app">
    <PulseLoader v-if="isLoading" class="loader" />
    <div v-else>
      <h2>Список пользователей</h2>
      <div class="user-list__header">
        <span class="header__name" @click="sortUserByName">Имя</span>
        <span class="header__phone">Телефон</span></div>
      <UserList :userList="userList" />
      <button @click="showAddNewUserModal" class="add-new-user-button">
        <span>Добавить пользователя</span>
      </button>
    </div>
    <AddNewUserModal
      @handleModalClose="hideAddNewUserModal"
      @addNewUser="addNewUser"
      v-if="isAddNewUserModalVisible"
      :userList="extendedUserList"
    />
  </div>
</template>

<script>
import axios from 'axios'
import { v4 as uuidv4 } from 'uuid'
import { propertyOf, cloneDeep, assign } from 'lodash'

import UserList from './components/UserList.vue'
import AddNewUserModal from './components/AddNewUserModal.vue'
import PulseLoader from 'vue-spinner/src/PulseLoader.vue'

export default {
  name: 'App',
  components: {
    UserList,
    AddNewUserModal,
    PulseLoader
  },
  data () {
    return {
      userList: [],

      extendedUserList: [],

      isAddNewUserModalVisible: false,

      isLoading: false
    }
  },

  created () {
    this.isLoading = true

    // Если список пользователей есть в localStorage возьмем его оттуда
    const cacheUserList = window.localStorage.getItem('userList')
    const cacheExtenderUserList = window.localStorage.getItem('extendedUserList')

    if (cacheUserList) {
      this.userList = JSON.parse(cacheUserList)
      this.extendedUserList = JSON.parse(cacheExtenderUserList)
      this.isLoading = false
      return
    }

    // Если данных нет - запросим их
    this.getUserData()
  },

  methods: {
    showAddNewUserModal () {
      this.isAddNewUserModalVisible = true
    },

    hideAddNewUserModal () {
      this.isAddNewUserModalVisible = false
    },

    addNewUser (newUserData) {
      const userParentId = propertyOf(newUserData)(['parentOfUser'])

      // Обновим список юзеров без вложенностей
      this.extendedUserList.push(newUserData)
      window.localStorage.setItem(
        'extendedUserList',
        JSON.stringify(this.extendedUserList)
      )

      if (userParentId) {
        const indexOfParentUser = this.userList.findIndex(
          el => el.id === userParentId
        )

        // Если элемент родителя не найден - добавим юзера в общий список
        if (indexOfParentUser < 0) {
          this.userList.push(newUserData)

          return
        }

        const userListClone = cloneDeep(this.userList)
        userListClone[indexOfParentUser] = assign(
          userListClone[indexOfParentUser],
          {
            childrens:
              userListClone.childrens && userListClone.childrens.length
                ? userListClone.childrens.push(newUserData)
                : [newUserData]
          }
        )
        this.userList = userListClone
      } else {
        this.userList.push(newUserData)
      }

      // Обновим данные в localStorage
      window.localStorage.setItem('userList', JSON.stringify(this.userList))
      this.hideAddNewUserModal()
    },

    sortUserByName () {
      const sortedList = this.userList.sort((a, b) => a.name.localeCompare(b.name))
      this.userList = sortedList
    },

    async getUserData () {
      try {
        const response = await axios.get(
          'https://jsonplaceholder.typicode.com/users'
        )

        if (!propertyOf(response)(['data'])) {
          this.userList = []

          return
        }

        const formatedUserList = propertyOf(response)(['data']).map(el => {
          return {
            name: el.name,
            phone: el.phone.split(' ').shift(),
            id: uuidv4()
          }
        })

        this.userList = formatedUserList
        window.localStorage.setItem(
          'userList',
          JSON.stringify(formatedUserList)
        )

        // Отдельно сохраним список пользователей без вложенностей
        window.localStorage.setItem(
          'extendedUserList',
          JSON.stringify(this.userList)
        )
        this.extendedUserList = cloneDeep(this.userList)
        this.isLoading = false
      } catch (error) {
        console.log(error)
      }
    }
  }
}
</script>

<style>
* {
  padding: 0;
  margin: 0;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: relative;
  margin: 0 auto;
}

.loader {
  margin-top: 300px;
}

h2 {
  padding-top: 100px;
}

.user-list__header {
  display: flex;
  width: 400px;
  justify-content: space-between;
  margin: 30px auto 0;
  padding-bottom: 15px;
  border-bottom: 1px solid #d3d3d3;
}

.add-new-user-button {
  border: none;
  width: 100%;
  max-width: 400px;
  font-size: 20px;
  color: #ffffff;
  background: #3083dc;
  border-radius: 10px;
  margin: 20px 0;
  padding: 10px 0;
}

.add-new-user-button:hover {
  cursor: pointer;
  background: rgb(20, 87, 159);
}

.header__name:hover {
  cursor: pointer;
}
</style>
