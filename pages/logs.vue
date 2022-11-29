<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card elevation="2" id="logs-container-card">
        <form @submit.prevent="searchForLogs">
          <v-text-field v-model="query" placeholder="service_id:1 AND message:healthcheck" label="query"></v-text-field>
          <v-text-field v-model="since" placeholder="how much seconds ago" label="since"></v-text-field>
          <v-btn type="submit">Search</v-btn>
        </form>
        <v-card dark class="logs-card">
          <div v-for="log in logs" class="logs-container">
            <span>
              <span class="blue-text">(service_name: {{ log.service_name }})</span>
              - <span class="green-text">(filename: {{ log.filename }})</span>: -
              <span class="yellow-text">(timestamp: {{ timeSince(log.created_at) }})</span>:
              {{ log.message }}
            </span>
          </div>
        </v-card>

      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import client from '../commons/client'

export default {
  name: 'LogsPage',
  data() {
    return { query: "", since: 60, logs: [] }
  },
  mounted() {
    this.logModal = document.getElementById('log-modal')
  },
  methods: {

    timeSince(date) {

      var seconds = Math.floor((new Date() - date) / 1000);

      var interval = seconds / 31536000;

      if (interval > 1) {
        return Math.floor(interval) + " years ago";
      }
      interval = seconds / 2592000;
      if (interval > 1) {
        return Math.floor(interval) + " months ago";
      }
      interval = seconds / 86400;
      if (interval > 1) {
        return Math.floor(interval) + " days ago";
      }
      interval = seconds / 3600;
      if (interval > 1) {
        return Math.floor(interval) + " hours ago";
      }
      interval = seconds / 60;
      if (interval > 1) {
        return Math.floor(interval) + " minutes ago";
      }
      return Math.floor(seconds) + " seconds ago";
    },


    searchForLogs() {
      const that = this
      var fullQuery = this.since ? (`${this.query} AND created_at:>${Date.now() - 1000 * parseInt(this.since)}`) : this.query
      console.log("Searching for logs... Query: " + fullQuery)


      client.get("/logs?query=" + fullQuery)
        .then((response) => {
          that.logs = response.data.hits.sort(function (a, b) { return b.created_at - a.created_at })
          that.logs
        }).catch((e) => {
          console.error(e)
          that.addEditDialog.active = false
          const message = e.response.data.message
          that.logModal.open(e.name, message)
        })
    }
  }
}
</script>

<style @scoped lang="scss">
#logs-container-card {
  background-color: $primary-color;
  padding: 30px;
  width: 120%;
}

.blue-text {
  color: lightblue
}

.green-text {
  color: green
}

.yellow-text {
  color: yellow
}

.logs-card {
  height: 500px;
  overflow-y: scroll;
}
</style>