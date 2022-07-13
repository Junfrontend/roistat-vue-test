<template>
  <div class="list-item" :id="user.id">
    <div class="list-item__content-wrapper">
      <span>{{ user.name }}</span>
      <span>{{ user.phone }}</span>
    </div>
    <UserList
      v-if="user.childrens"
      :userList="user.childrens"
      class="list-item__sublist"
    />
  </div>
</template>

<script>
export default {
  components: {
    UserList: () => import('./UserList.vue')
  },
  props: {
    user: {
      default () {
        return {}
      },
      type: Object
    }
  }
}
</script>

<style>
.list-item__content-wrapper {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 0;
  border-bottom: 1px solid #e4e4e4;
}

.list-item__sublist {
  position: relative;
  left: 20px;
}
</style>
