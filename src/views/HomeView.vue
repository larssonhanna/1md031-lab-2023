<template>
  <header>
    <img id="headerimg"
      src="https://tb-static.uber.com/prod/image-proc/processed_images/f90558da74dc7fc90080a418d99c5758/97e6648b3593c29cb4a6335f976e6d84.jpeg"
      alt="Header" title="Header">
    <h1 id="headline">Hannas Hamburgare!</h1>
  </header>
  <main>
    <section id="meny">
      <h2>Meny</h2>
      <p>För nuvarande finns 3 burgare på menyn</p>
      <div class="wrapper">
        <Burger v-for="burger in burgers" :key="burger.name" :burger="burger" @orderedBurgers="updateOrderedBurgers" />
      </div>
    </section>
    <section id="info">
      <h2>Beställningsinformation</h2>
      <p>Vänligen fyll i din information</p>
      <p>
        <label for="name">Namn</label><br>
        <input v-model="formData.name" type="text" id="name" name="n" required="required" placeholder="För- och efternamn">
      </p>
      <p>
        <label for="email">E-post</label><br>
        <input v-model="formData.email" type="email" id="email" name="em" required="required" placeholder="exempel@gmail.com">
      </p>
      <p>
        <label for="adress">Gatunamn</label><br>
        <input v-model="formData.address" type="text" id="adress" name="ad" required="required" placeholder="Exempelgatan">
      </p>
      <p>
        <label for="no">Husnummer</label><br>
        <input v-model="formData.houseNumber" type="number" id="no" name="no" required="required" placeholder="0">
      </p>
      <p>
        <label for="betalsätt">Betalsätt</label>
        <select v-model="formData.paymentMethod" id="betalsätt" name="betal">
          <option>Kort</option>
          <option selected="selected">Swish</option>
          <option>Apple pay</option>
          <option>Klarna</option>
        </select>
      </p>
      <p>
      <form>
        <p>Kön:</p>
        <input v-model="formData.gender" type="radio" id="male" name="gender" value="male">
        <label for="male">Man</label><br>

        <input v-model="formData.gender" type="radio" id="female" name="gender" value="female">
        <label for="female">Kvinna</label><br>

        <input v-model="formData.gender" type="radio" id="other" name="gender" value="other" checked>
        <label for="other">Vill inte ange</label><br>
      </form>
      </p>
    </section>

    <div id="map" v-on:click="addOrder">
      click here
    </div>

    <button v-on:click="submitForm" type="button" class="custom-button">
      <img src="https://burgerking.se/assets/images/food-menu-placeholder.webp" style="width: 35px;">
      Skicka beställning
    </button>
  </main>
  <footer>
    <hr>
  </footer>
  <div>
    <div>
      <h1>Burgers</h1>
      <!--  <Burger v-for="burger in burgers" 
              v-bind:burger="burger" 
              v-bind:key="burger.name"/> -->
    </div>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import menu from '../assets/menu.json'
import io from 'socket.io-client'

const socket = io();

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      formData: {
        name: '',
        email: '',
        address: '',
        houseNumber: '',
        paymentMethod: 'Swish',
        gender: 'other',
        orderedBurgers: [],
        location: {
        x: 0,
        y: 0
      },
      },
    }
    },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random() * 100000);
    },
    addOrder: function (event) {
      
  var offset = {
    x: event.currentTarget.getBoundingClientRect().left,
    y: event.currentTarget.getBoundingClientRect().top
  };

  // Get the clicked coordinates relative to the #map container
  const clickedX = event.clientX - 10 - offset.x;
  const clickedY = event.clientY - 10 - offset.y;

  // Append a black box to #map at the clicked location
  const dot = document.createElement('div');
  dot.className = 'dot';
  dot.style.left = `${clickedX}px`;
  dot.style.top = `${clickedY}px`;
  document.getElementById('map').appendChild(dot);

  // Update the location in formData
  this.formData.location = {
    x: clickedX,
    y: clickedY
  };

  // Emit the order to the server
  socket.emit("addOrder", {
    orderId: this.getOrderNumber(),
    details: {
      x: clickedX,
      y: clickedY
    },
    orderItems: this.formData.orderedBurgers,
  });
},
    updateOrderedBurgers(burger) {
      // Update formData.orderedBurgers based on the emitted data
      const existingBurger = this.formData.orderedBurgers.find(b => b.name === burger.name);

      if (existingBurger) {
        existingBurger.amount = burger.amount;
      } else {
        this.formData.orderedBurgers.push({
          name: burger.name,
          amount: burger.amount
        });
      }
    },
    submitForm() {
      console.log('Form Data:', this.formData);
  },
},
}
</script>

<style>
#map {
  width: 1920px;
  height: 1078px;
  background: url("/Users/hannalarsson/Documents/Granssnitt/Lab1/1md031-lab-2023/public/img/polacks.jpg");
  overflow: scroll;
}

.dot{
    background: black;
    color: white;
    width:20px;
    height:20px;
    border-radius: 10px;
    text-align: center;
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    cursor: crosshair;
}

.custom-button {
  font-size: 1.5em;
}

body {
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}

.ing {
  font-weight: bold;
}

#meny {
  color: antiquewhite;
  background-color: black;
  border: 4px double;
  border-color: antiquewhite;
  border-radius: 25px;
}

#info {
  border: 4px double;
  border-color: black;
  border-radius: 25px;
}

button:hover {
  background-color: greenyellow;
  cursor: pointer;
}

section {
  margin: 20px;
}

button {
  margin: 30px 20px;
}

div {
  padding: 10px;
  margin: 10px;
}

p {
  margin-left: 10px;
}

h2 {
  margin-left: 10px;
}

form {
  margin-left: 10px;
}

header {
  margin: 20px;
  height: 200px;
  overflow: hidden;
  border-radius: 25px;
}

#headerimg {
  opacity: 0.8;
  width: 100%;
  height: auto;
  object-fit: cover;
}

header>h1 {
  position: absolute;
  width: 100%;
  margin: 0 auto;
  top: 100px;
  left: 0px;
  text-align: center;
}

.wrapper {
  display: grid;
  grid-template-columns: 33% 33% 33%;
}</style>