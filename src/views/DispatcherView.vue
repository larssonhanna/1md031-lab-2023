<template>
  <div id="orders">
    <div id="orderList">
      <div class="oneOrder" v-for="(order, key) in orders" v-bind:key="'order' + key">
        <div v-for="(burgerData, index) in order.customer.orderItems" :key="index">
          <p class="burgare" v-if="burgerData.amount > 0">{{ burgerData.name }}: {{ burgerData.amount }}</p>
        </div>
        Order no: {{ key }} <br>
        Customer Information: <br>
        Name: {{ order.customer.name }}, Email: {{ order.customer.email }} <br>
        Payment: {{ order.customer.paymentMethod }}, Gender: {{ order.customer.gender }}
        <hr>
      </div>
      <button v-on:click="clearQueue">Clear Queue</button>
    </div>
    <div id="dots" v-bind:style="{ background: 'url(' + require('../../public/img/polacks.jpg') + ')' }">
      <div v-for="(order, key) in orders" v-bind:style="{ left: order.details.x + 'px', top: order.details.y + 'px' }"
        v-bind:key="'dots' + key">
        {{ key }}
      </div>
    </div>
  </div>
</template>
<script>
import io from 'socket.io-client'
const socket = io();

export default {
  name: 'DispatcherView',
  data: function () {
    return {
      orders: null,
      orderNumber: 0,
    }
  },
  created: function () {
    socket.on('currentQueue', data =>
      this.orders = data.orders);
  },
  methods: {
    clearQueue: function () {
      socket.emit('clearQueue');
      this.orderNumber = 0;
    }
  }
}
</script>
<style>
#orderList {
  top: 1em;
  left: 1em;
  position: absolute;
  z-index: 2;
  color: black;
  background: rgba(255, 255, 255, 0.5);
  padding: 1em;
  margin: 0;
}

#dots {
  position: relative;
  margin: 0;
  padding: 0;
  background-repeat: no-repeat;
  width: 1920px;
  height: 1078px;
  cursor: crosshair;
}

#dots div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}

.oneOrder {
  margin: 30px;
  padding: 0;
  position: relative;
}

.burgare {
  margin: 0;
  padding: 0;
  font-weight: bold;
}
</style>
  