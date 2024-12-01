<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="(order, index) in orders" :key="'order'+index">
          <h4>Order {{ index + 1 }}</h4>
          <ul>
            <li v-for="item in order.orderItems" :key="item.name">
            {{ item.name }} ({{ item.amount }} st)
            </li>
          </ul>
          <p>Namn: {{ order.customerInfo.firstname }}</p>
          <p>E-mail: {{ order.customerInfo.email }}</p>
          <p>Betalningsalternativ: {{ order.customerInfo.payment }}</p>
          <p>Kön: {{ order.customerInfo.gender }}</p>
          <hr>
        </div>
        <button v-on:click="clearQueue">Clear Queue</button>
      </div>
      <div id="dots">
          <div v-for="(order, key) in orders" v-bind:style="{ left: order.details.x + 'px', top: order.details.y + 'px'}" v-bind:key="'dots' + key">
            {{ key }}
          </div>
      </div>
    </div>
  </template>
  <script>
  import io from 'socket.io-client'
  const socket = io("localhost:3000");
  
  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: [],
      }
    },
    created: function () {

      socket.on('currentQueue', data =>
        this.orders = data.orders);
    },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId: orderId, status: "Annan status"});

      },
    }
  }
  </script>
  <style>

  #orderList {
    top:1em;
    left:1em;
    position: absolute;
    z-index: 2;
    color:black;
    background: rgba(255,255,255, 0.5);
    padding: 1em;
  }
  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url('/img/polacks.jpg');
  }
  
  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
  </style>
  