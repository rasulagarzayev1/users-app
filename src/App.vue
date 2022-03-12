<template>
  <Header />
  <UserList />
</template>

<script>
import Header from './components/Header.vue';
import UserList from './components/UserList.vue';

export default {
  name: 'App',
  components: {
    Header,
    UserList,
  }
}
</script>
