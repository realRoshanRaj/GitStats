<template>
  <v-layout>
    <v-flex class="text-center">
      <img
        src=""
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
    </v-flex>
  </v-layout>
</template>

<script>
  export default {
    data: () => ({
      searchQuery: ''
    }),
    methods: {
      search() {
        this.$router.push('/users/' + this.searchQuery)
      }
    }
  }
</script>
