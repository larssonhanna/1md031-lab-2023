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
        <Burger v-for="burger in burgers" :key="burger.name" :burger="burger" @orderedBurgers="updateOrderedBurgers" ref="burgers" />
      </div>
    </section>
    <section id="info">
      <h2>Beställningsinformation</h2>
      <p>Vänligen fyll i din information</p>
      <p>
        <label for="name">Namn</label><br>
        <input v-model="formData.name" type="text" id="name" name="n" required="required"
          placeholder="För- och efternamn">
      </p>
      <p>
        <label for="email">E-post</label><br>
        <input v-model="formData.email" type="email" id="email" name="em" required="required"
          placeholder="exempel@gmail.com">
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
        <input v-model="formData.gender" type="radio" id="Man" name="gender" value="Man">
        <label for="Man">Man</label><br>

        <input v-model="formData.gender" type="radio" id="Kvinna" name="gender" value="Kvinna">
        <label for="Kvinna">Kvinna</label><br>

        <input v-model="formData.gender" type="radio" id="Vill inte ange" name="gender" value="Vill inte ange" checked>
        <label for="Vill inte ange">Vill inte ange</label><br>
      </form>
      </p>
    </section>

Vänligen markera din leveransadress på kartan nedan:
    <div id="map" v-on:click="setLocation" style="position:relative">
      <div id="target"
        v-bind:style="{ position: 'absolute', left: formData.location.x + 'px', top: formData.location.y + 'px' }">
        <div class="dot">T</div>
      </div>
    </div>

    <button v-on:click="addOrder()" type="button" class="custom-button">
      <img src="https://burgerking.se/assets/images/food-menu-placeholder.webp" style="width: 35px;">
      Skicka beställning
    </button>
  </main>
  <footer>
    <hr>
    Interface Programming with a User Perspective 2023
  </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import menu from '../assets/menu.json'
import io from 'socket.io-client'

const socket = io();

// never used
function MenuItem(name, url, hasLactose, hasGluten){
    this.name = name;
    this.url = url;
    this.hasGluten = hasGluten;
    this.hasLactose = hasLactose;
}

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
        paymentMethod: 'Swish',
        gender: 'Vill inte ange',
        orderedBurgers: [],
        location: {
          x: 0,
          y: 0
        },
      },
      orderNumber: 0,
    }
  },
  methods: {
    setLocation: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      this.formData.location = {
        x: event.clientX - 40 - offset.x,
        y: event.clientY - 40 - offset.y
      }
    },
    
    addOrder: function () {

      if (this.formData.orderedBurgers.length === 0) {
      alert("Du måste beställa minst en burgare");
      return;
    }

      console.log(this.formData)
      this.orderNumber++;

      // Emit the order to the server
      socket.emit("addOrder", {
        orderId: this.orderNumber,
        details: {
          x: this.formData.location.x,
          y: this.formData.location.y
        },
        customer: {
          name: this.formData.name,
          email: this.formData.email,
          paymentMethod: this.formData.paymentMethod,
          gender: this.formData.gender,
          orderItems: this.formData.orderedBurgers,
        },
      });
      alert("Beställning skickad!");

      // Reset form data
    this.formData = {
      name: '',
      email: '',
      paymentMethod: 'Swish',
      gender: 'Vill inte ange',
      orderedBurgers: [],
      location: {
        x: 0,
        y: 0
      },
    };
    this.$refs.burgers.forEach(burgerComponent => {
      burgerComponent.resetBurgerNumbers();
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

.dot {
  background: black;
  color: white;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  text-align: center;
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
}
</style>