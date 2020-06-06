<template>
  <v-container>
    <v-row>
      <v-col cols="3">
        <v-card color="transparent">
          <v-img :src="pfpURL"></v-img>
          <a class="white--text" target="_blank" :href="userPageURL"><h2>{{name}}</h2></a>
        </v-card>
      </v-col>
<!--      <v-spacer></v-spacer>-->
      <v-col cols="6">
        <chartjs-doughnut
          :bind="true"
          :datasets="datasets"
          :labels="labels"
          :option="{}"
        />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  import axios from 'axios';

  export default {
    async asyncData({params}) {
      const {data} = await axios.get(('https://api.github.com/users/' + params.id));
      console.log(data);
      let name = data.name;
      if (name === null) name = data.login;
      return {userId: params.id, pfpURL: data.avatar_url, name, userPageURL: data.html_url, data};
    },
    data: () => ({
      userId: '',
      pfpURL: '',
      name: '',
      userPageURL: '',
      data: {},
      datasets: [
        {
          data: [100, 20, 40],
          backgroundColor: ["#f36e60", "#ffdb3b", "#185190"],
        }
      ],
      labels: ["Foo", "Bar", "Baz"]
    }),
    methods: {}

  }

</script>
