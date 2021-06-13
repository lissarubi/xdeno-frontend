<template>
  <div class="user">
    <h1>{{ user.name }}</h1>
    <h2>Pings totais: {{ user.totalPings }}</h2>
    <div class="scrollableTable">
      <table>
        <tr>
          <th>Canal</th>
          <th>Data</th>
        </tr>
        <tr v-for="ping in user.pings" :key="ping._id">
          <th>
            {{ ping.channel }}
          </th>
          <th>
            {{ ping.date }}
          </th>
        </tr>
      </table>
    </div>

    <div class="scrollableTable">
      <table>
        <tr>
          <th>Canal</th>
          <th>Vezes</th>
        </tr>
        <tr v-for="channel in frequency" :key="channel">
          <th>
            {{ channel[0] }}
          </th>
          <th>
            {{ channel[1] }}
          </th>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Streamer",

  data() {
    return {
      user: {},
      BACKEND_URL: process.env.VUE_APP_BACKEND_URL,
      name: this.$route.params.user,
      frequency: [],
    };
  },
  async mounted() {
    let req = await axios.get(this.BACKEND_URL + "/user/" + this.name);

    req.data.forEach((ping, index) => {
      let date = new Date(ping.date).toLocaleString("pt-BR");
      req.data[index].date = date;
    });

    req.data = req.data.reverse();

    if (req.status == 200) {
      this.user = {
        name: this.name,
        totalPings: req.data.length,
        pings: req.data,
      };

      let channelsNames = req.data.map((ping) => {
        return ping.channel;
      });

      this.frequency = Object.entries(
        channelsNames.reduce((a, c) => ((a[c] = (a[c] || 0) + 1), a), {})
      ).sort((a, b) => {
        return b[1] - a[1];
      });
    }
  },
};
</script>
