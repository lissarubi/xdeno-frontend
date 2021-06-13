<template>
  <div class="home">
    <h1>XDeno</h1>

    <div class="pings">
      <div class="scrollableTable">
        <table>
          <tr>
            <th>Nome</th>
            <th>Pings</th>
          </tr>
          <tr v-for="user in userPings" :key="user.name">
            <th>
              <a :href="'/user/' + user.name">
                {{ user.name }}
              </a>
            </th>
            <th>
              <a :href="'/user/' + user.name">{{ user.totalPings }}</a>
            </th>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Home",

  data() {
    return {
      BACKEND_URL: process.env.VUE_APP_BACKEND_URL,
      usernames: [],
      userPings: [],
    };
  },
  async mounted() {
    let reqRoot = await axios.get(this.BACKEND_URL + "/");

    let preUsersPings = [];

    reqRoot.data.forEach((ping) => {
      if (this.usernames.indexOf(ping.user) == -1) {
        this.usernames.push(ping.user);
      }
    });
    this.usernames.forEach(async (user) => {
      if (user != "") {
        let req = await axios.get(this.BACKEND_URL + "/user/" + user);

        preUsersPings.push({
          pings: req.data,
          totalPings: req.data.length,
          name: user,
        });

        this.userPings = preUsersPings.sort((a, b) => {
          return b.totalPings - a.totalPings;
        });
      }
    });
  },
};
</script>

<style>
@import "styles.css";
</style>
