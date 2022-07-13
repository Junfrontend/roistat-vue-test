<template>
  <div class="user-list">
    <ListItem
      v-for="user in userList"
      :user="user"
      :key="user.name"
    />
  </div>
</template>

<script>
import ListItem from './ListItem.vue'

export default {
  name: 'UserList',
  components: { ListItem },
  props: {
    userList: {
      type: Array,
      required: true
    }
  }
}
</script>

<style scoped>
.user-list {
  max-width: 400px;
  margin: 0 auto;
}
</style>
