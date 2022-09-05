<template>
  <the-header/>
  <the-error v-show="!servers"/>
  <div class="container">
    <servers-table :servers="servers"/>
    <update-time :updated="updated"/>
    <!-- <servers-card :servers="servers"/> -->
  </div>
  <the-footer/>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, onBeforeUnmount } from 'vue';
import axios from 'axios';

import TheHeader from '@/components/TheHeader.vue';
import TheError from '@/components/TheError.vue';
import ServersTable from '@/components/ServersTable.vue';
import UpdateTime from '@/components/UpdateTime.vue';
// import ServersCard from '@/components/ServersCard.vue';
import TheFooter from '@/components/TheFooter.vue';

export default defineComponent({
  name: 'App',
  components: {
    TheHeader,
    TheError,
    ServersTable,
    // ServersCard,
    TheFooter,
    UpdateTime
  },
  setup() {
    const servers = ref<Array<StatusItem | BoxItem>>();
    const updated = ref<number>();
    const { interval } = window.__PRE_CONFIG__;
    let timer: number;
    const runFetch = () => axios.get('json/stats.json')
      .then(res => {
        servers.value = res.data.servers;
        updated.value = Number(res.data.updated);
      })
      .catch(err => console.log(err));
    onMounted(() => runFetch() && (timer = setInterval(runFetch, interval * 1000)));
    onBeforeUnmount(() => clearInterval(timer));
    return {
      servers,
      updated
    };
  }
});
</script>

<style>
body {
  /*Replace your background image at this place!*/
  background: url("./assets/img/bg_parts.png") repeat-y left top, url('./assets/img/bg.png') repeat left top;
}

/*Global*/
div.bar {
  min-width: 0 !important;
}

/*Responsive*/
@media only screen and (min-width: 1200px) {
  .container {
    width: 1255px;
    margin: 0 auto;
  }
}

@media only screen and (max-width: 1200px) {
  #app .container {
    width: auto;
    margin: 0 .8rem;
  }

  #table thead tr th, #table tr.tableRow td {
    padding: .7em;
  }
}

@media only screen and (max-width: 1100px) {
  #type, tr td:nth-child(3) {
    display: none;
  }
}

@media only screen and (max-width: 1000px) {
  html, body {
    font-size: 13px;
  }
}

@media only screen and (max-width: 950px) {
  #location, tr td:nth-child(4) {
    display: none;
  }
  #ct, tr td:nth-child(12) {
    display: none;
  }
  #cu, tr td:nth-child(13) {
    display: none;
  }
  #cm, tr td:nth-child(14) {
    display: none;
  }
}

@media (max-width: 800px) {
  html, body {
    font-size: 12px;
  }

  #servers div.progress {
    width: 40px;
  }

  #cards .card div.card-header span {
    font-size: 1.55rem;
  }

  #cards .card div.card-content p {
    font-size: 1.25rem;
    margin-bottom: 0.6rem;
  }

  #app #header {
    height: 20rem;
    /*Replace your header image (for mobile use) at this place!*/
    background: url("assets/img/cover_mobile.png") no-repeat center center !important;
  }
}

@media only screen and (max-width: 780px) {
  #uptime, tr td:nth-child(5) {
    display: none;
  }
}

@media only screen and (max-width: 690px) {
  #load_1, tr td:nth-child(6) {
    display: none;
  }
}

@media only screen and (max-width: 630px) {
  #traffic, tr td:nth-child(8) {
    display: none;
  }
}

@media only screen and (max-width: 560px) {
  #name, tr td:nth-child(2) {
    min-width: 20px;
    max-width: 60px;
    text-overflow: ellipsis;
    overflow: hidden;
  }

  #hdd, tr td:nth-child(11) {
    display: none;
  }

  #cpu, #ram {
    min-width: 20px;
    max-width: 40px;
  }
}
</style>
