<template>
  <div>
    <div>
      <h1>Burgers</h1>
      <Burger v-for="burger in burgers"
              v-bind:burger="burger" 
              v-bind:key="burger.name"/>
    </div>
    <div id="map" v-on:click="addOrder">
      click here
    </div>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io();

function MenuItem(name, url, hasLactose, hasGluten){
    this.name = name;
    this.url = url;
    this.hasGluten = hasGluten;
    this.hasLactose = hasLactose;
}

const Burgers = [
  {
    name: "Klassisk ostburgare",
    url: "https://cdn-bk-se-ordering.azureedge.net/media/ywfd4uqs/cheesy-cheese.png",
    hasLactose: true,
    hasGluten: true
  },
  {
    name: "Dubbelburgare med bacon",
    url: "https://cdn-bk-se-ordering.azureedge.net/media/rvndqsz4/double-gourmet-grill-steakhouse.png",
    hasLactose: true,
    hasGluten: true
  },
  {
    name: "Krispig kycklingburgare",
    url: "https://cdn-bk-se-ordering.azureedge.net/media/tzdfpoe0/bk_kiosk_400x290_singel_crispychicken.png",
    hasLactose: false,
    hasGluten: true
  }
];

console.log(Burgers);

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: Burgers
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    }
  }
}
</script>

<style>
  #map {
    width: 300px;
    height: 300px;
    background-color: red;
  }
</style>