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
        <Doughnut :dataset="langDatasets" :labels="langLabels"
                  :options="options"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  import axios from 'axios';
  import Doughnut from "../../components/Doughnut";

  export default {
    components: {
      Doughnut
    },
    async asyncData({params}) {
      const {data} = await axios.get(('https://api.github.com/users/' + params.id));
      console.log(data);
      let name = data.name;
      if (name === null) name = data.login;
      const {data: repos} = await axios.get(data.repos_url);
      const langData = {};
      for (let i = 0; i < repos.length; i++) {
        const {data: lang} = await axios.get(repos[i].languages_url);
        for (const prop in lang) {
          if (langData.hasOwnProperty(prop)) {
            langData[prop] += lang[prop];
          } else {
            langData[prop] = lang[prop]
          }
        }
      }
      const langDataset = [{
        data: Object.values(langData)
      }]
      return {
        userId: params.id,
        pfpURL: data.avatar_url,
        name,
        userPageURL: data.html_url,
        data,
        repos,
        langLabels: Object.keys(langData),
        langDatasets: langDataset
      };
    },
    data: () => ({
      userId: '',
      pfpURL: '',
      name: '',
      userPageURL: '',
      data: {},
      repos: [],
      langDatasets: [
        {
          data: [],
        }
      ],
      langLabels: [],
      options: {
        legend: {display: true, position: 'right', fontColor: '#ffffff'},
        title: {text: 'Languages', display: true, fontColor: '#ffffff', fontSize: 16,},
        tooltips: {
          callbacks: {
            label: function (tooltipItem, data) {
              //get the concerned dataset
              const dataset = data.datasets[tooltipItem.datasetIndex];
              //calculate the total of this data set
              const total = dataset.data.reduce(function (previousValue, currentValue, currentIndex, array) {
                return previousValue + currentValue;
              });
              //get the current items value
              const currentValue = dataset.data[tooltipItem.index];
              //calculate the precentage based on the total and current item, also this does a rough rounding to give a whole number
              const percentage = Math.floor(((currentValue / total) * 100) + 0.5);

              return data['labels'][tooltipItem['index']] + ': ' + percentage + "%";
            }
          }
        }
      }
    }),
    methods: {}

  }

</script>
