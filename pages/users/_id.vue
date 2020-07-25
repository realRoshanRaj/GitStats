<template>
  <v-container>
    <v-row>
      <v-col cols="3">
        <v-card color="transparent">
          <a class="white--text" target="_blank" :href="userPageURL">
            <v-img :src="pfpURL"></v-img>
            <h2>{{name}}</h2>
          </a>
        </v-card>
      </v-col>
      <v-col cols="7">
        <Doughnut :dataset="langDatasets" :labels="langLabels"
                  :options="options"/>
      </v-col>
    </v-row>
    <BarChart :label="'Commits Per Repo'" :labels="reposNames" :data="commitsPerRepo"
              :options="{legend: {display: false,}, title: {display: true, text: 'Commits Per Repository', fontColor: '#ffffff', fontSize: 16,}}"/>
  </v-container>
</template>

<script>
  import axios from 'axios';
  import Doughnut from "../../components/Doughnut";
  import BarChart from "../../components/BarChart";

  export default {
    components: {
      Doughnut,
      BarChart
    },
    async asyncData({params}) {
      const {data} = await axios.get(('https://api.github.com/users/' + params.id));
      console.log(data);
      let name = data.name;
      if (name === null) name = data.login;
      const {data: repos} = await axios.get(data.repos_url);

      // Lang Doughnut
      const langData = {};
      const reposNames = [];
      const commitsPerRepo = [];
      for (let i = 0; i < repos.length; i++) {
        const {data: lang} = await axios.get(repos[i].languages_url);
        const {data: commits} = await axios.get(repos[i].url + `/commits?author=${data.login}`);
        reposNames.push(repos[i].name);
        commitsPerRepo.push(commits.length);
        for (const prop in lang) {
          if (langData.hasOwnProperty(prop)) {
            langData[prop] += lang[prop];
          } else {
            langData[prop] = lang[prop]
          }
        }
      }
      const langDataset = [{
        data: Object.values(langData),
        borderColor: '#000'
      }]


      return {
        userId: params.id,
        pfpURL: data.avatar_url,
        name,
        userPageURL: data.html_url,
        data,
        repos,
        langLabels: Object.keys(langData),
        langDatasets: langDataset,
        reposNames,
        commitsPerRepo
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
        legend: {display: true, position: 'right', labels: {fontColor: '#ffffff'}},
        title: {text: 'Languages', display: true, fontColor: '#ffffff', fontSize: 16,},
        // tooltips: {
        // callbacks: {
        // label: function (tooltipItem, data) {
        //   //get the concerned dataset
        //   const dataset = data.datasets[tooltipItem.datasetIndex];
        //   //calculate the total of this data set
        //   const total = dataset.data.reduce(function (previousValue, currentValue, currentIndex, array) {
        //     return previousValue + currentValue;
        //   });
        //   //get the current items value
        //   const currentValue = dataset.data[tooltipItem.index];
        //   //calculate the precentage based on the total and current item, also this does a rough rounding to give a whole number
        //   const percentage = Math.floor(((currentValue / total) * 100) + 0.5);
        //
        //   return data['labels'][tooltipItem['index']] + ': ' + percentage + "%";
        // }
        // }
        // }
      },
      reposNames: [],
      commitsPerRepo: []
    }),
    methods: {}

  }

</script>
