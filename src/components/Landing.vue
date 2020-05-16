<template class="base-template">
  <div>
    <h1>Welcome to Domains App</h1>
    <h2>Here you can find out information about domains</h2>
    <div class="main-container">
      <b-input-group prepend="Domain name" class="mt-3">
        <b-form-input v-model="inputValue" @keyup.enter="findDomain(inputValue)"></b-form-input>
        <b-input-group-append>
          <b-button variant="outline-info" @click="findDomain(inputValue)">Search</b-button>
          <b-button variant="dark" @click="allDomains()">Domain history</b-button>
        </b-input-group-append>
      </b-input-group>
      <h2 class="main-container" v-if="warningDomain">{{msg}}</h2>
    </div>
    <div v-if ="showCards" class="main-container">
      <div></div>
      <b-card
          border-variant="secundary"
          :header= "domain.Title"
          header-text-variant="white"
          align="center"
          header-bg-variant="info">
        <b-card-img class="img-style" :src="domain.Logo" @error="changeImage()"></b-card-img>
        <b-card-text>
          <span class="text-sub">Servers changed: </span> {{domain.Servers_changed}}<br>
          <span class="text-sub">SSL grade: </span> {{domain.Ssl_grade}}<br>
          <span class="text-sub">Previous SSL grade: </span>{{domain.Previous_ssl_grade}}<br>
          <span class="text-sub">Active: </span>{{domain.Is_down}}
        </b-card-text>
      </b-card>
      <b-card v-for="server in domain.Servers" :key="server.Address"
          border-variant="secundary"
          :header= "server.Address"
          header-bg-variant="dark"
          header-text-variant="white"
          align="center"
          class="card-style">
          <b-card-text>
            <span class="text-sub">SSL grade: </span> {{server.Ssl_grade}}<br>
            <span class="text-sub">Country: </span>{{server.Country}}<br>
            <span class="text-sub">Owner: </span>{{server.Owner}}
          </b-card-text>
      </b-card>
    </div>
    <div v-if="showTable" class="main-container">
      <b-table striped hover head-variant="dark" :items="domains"></b-table>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

export default {
  name: 'Landing',
  data () {
    return {
      ROOT_BACKEND: 'http://localhost:1206',
      inputValue: '',
      domain: '',
      domains: [],
      showCards: false,
      showTable: false,
      warningDomain: false,
      msg: '',
      domainMsg: ''
    }
  },
  methods: {
    async findDomain (inputValue) {
      this.showTable = false
      this.warningDomain = true
      this.showCards = false
      try {
        this.msg = 'Searching...'
        const res = await axios.get(`${this.ROOT_BACKEND}/domain/${inputValue.trim()}`)
        this.warningDomain = false
        this.domain = res.data
        if (this.domain.Logo === '') {
          this.domain.Logo = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/ac/No_image_available.svg/1200px-No_image_available.svg.png'
        }
        this.showCards = true
      } catch (error) {
        this.msg = 'Domain no found'
        this.warningDomain = true
        this.showCards = false
        console.log(error)
      }
    },
    async allDomains () {
      this.showCards = false
      this.warningDomain = false
      this.domains = []
      try {
        const res = await axios.get(`${this.ROOT_BACKEND}/domain`)
        if (res.data.Item != null) {
          res.data.Item.forEach(element =>
            this.domains.push({name: element})
          )
          console.log(this.domains)
          this.showTable = true
        } else {
          this.msg = 'No domain has been queried'
          this.warningDomain = true
        }
      } catch (error) {
        console.log(error)
      }
    }
  }
}
</script>

<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}

.base-template{
  font-family: "Roboto";
}

.main-container{
  margin: 20px;
  padding: 40px;
  width: 800px;
  margin: auto;
}

.card-style{
  margin-top: 20px;
}

.img-style{
  width: 50px;
  height: 50
}

.text-sub{
  font-weight: bold;
}

.box-header{
  background-color:#0097a7;
}

</style>
