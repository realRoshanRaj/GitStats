<template>
  <v-flex class="text-center">
    <img
      src="/logo.png"
      alt="Insert Logo Here"
      class="mb-5"
    >
    <v-form @submit.prevent="search">
      <v-col>
        <v-text-field
          v-model="searchQuery"
          label="Search"
          solo
          append-outer-icon="mdi-magnify"
          @click:append-outer="search"
        ></v-text-field>
      </v-col>
    </v-form>
    <v-card>
      <v-list v-if="users.length > 0" :avatar="true">
        <template v-for="(item, i) in users">
<!--          <v-list-item-group v-model="users.length">-->
            <v-list-item
              :key="i" @click="clickUser(item)">
              <v-list-item-avatar>
                <v-img :src="item.avatar_url"></v-img>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-list-item-title v-html="item.login"></v-list-item-title>
              </v-list-item-content>
            </v-list-item>
            <v-divider inset></v-divider>
<!--          </v-list-item-group>-->
        </template>
      </v-list>
    </v-card>

  </v-flex>
</template>

<script>
  import axios from 'axios';

  export default {
    data: () => ({
      searchQuery: '',
      users: []
    }),
    methods: {
      async search() {
        const {data} = await axios.get(`https://api.github.com/search/users?q=${this.searchQuery}`);
        this.users = data.items;
      },
      clickUser(user) {
        this.$router.push('/users/' + user.login);
      }
    }
  }
</script>
