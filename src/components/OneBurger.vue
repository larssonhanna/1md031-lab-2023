<template>
  <div class="box a">
    <h3>
      {{ burger.name }}
    </h3>
    <img v-bind:src="burger.url" :alt="burger.name" :title="burger.name" style="height: 200px;">
    <ul class="lista">
      <li v-if="burger.hasGluten">Innehåller <span class="ing">gluten</span></li>
      <li v-if="burger.hasLactose">Innehåller <span class="ing">laktos</span></li>
      <li>Kalorier: {{ burger.kCal }} kCal</li>
    </ul>
    <div class="order-container">
      <p>Antal:</p>
      <button v-on:click="removeFromOrder">-</button>
        <p>    {{ amountOrdered }}</p>
    <button v-on:click="addToOrder">+</button>
  </div>
  </div>

</template>
  
<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object,
  },
  data: function () {
  return {
    amountOrdered: 0,
  }
},
methods: {
    addToOrder() {
      this.amountOrdered += 1;
      this.$emit('orderedBurgers', {
      name: this.burger.name,
      amount: this.amountOrdered
    });
    },
    resetBurgerNumbers() {
      this.amountOrdered = 0;
    },
    removeFromOrder() {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
        this.$emit('orderedBurgers', {
        name: this.burger.name,
        amount: this.amountOrdered
      });
      }
    },
  },
}

</script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
 .ing {
    font-weight: bold;
 }
 .order-container {
  display: flex;
  align-items: center;
  border: 1px solid #ccc;
  padding: 5px;
  margin-right: 10px;
}

.order-container button{
  margin-right: 0px;
}

.lista{
  height: 100px;
}

</style>