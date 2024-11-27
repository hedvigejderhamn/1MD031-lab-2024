<template>

<header>
    <img src = "/img/bg.jpg">
    <h1> {{header}}</h1>
    </header>
    <main>
      <section id="burgers">
        <h3>Meny</h3>
        <div class="burger-grid">
          <OneBurger 
            v-for="burger in burgers" 
            :key="burger.name" 
            :burger="burger"
            v-on:orderedBurger="updateOrder($event)" />
        </div>           
      </section>
            
      <section id="info">
                <h3> Kundinformation </h3>
                <p> Fyll i följande uppgifter för en snabb och smidig leverans:</p>
                <form>
                <h4> Leveransuppgifter:</h4>
                <p>
                    <label for="firstnamne">Fullständigt namn</label><br>
                    <input type="text" id="firstname" v-model="formData.firstname" required="required" placeholder="För- och Efternamn">
                </p>
                <p>
                    <label for="email">E-mail</label><br>
                    <input type="text" id="email" v-model="formData.email" required="required" placeholder="E-mail adress">
                </p>
                <br>
                <p>
                    <label for="payment">Betalningsalternativ</label>
                    <select id="payment" v-model="formData.payment">
                    <option value="Kortbetalning">Kortbetalning</option>
                    <option value="Swish">Swish</option>
                    <option value="Klarna">Klarna</option>
                    <option value="Faktura">Faktura</option>
      </select>
                 </p>
                 <p> Ange kön: </p>
                 <p>
                    <input id="gender" v-model="formData.gender" type="radio" value="man">
                    <label for="gender">Man</label>
                 </p>
                 <p>
                    <input id="gender" v-model="formData.gender" type="radio" value="women">
                    <label for="gender">Kvinna</label>
                 </p>
                 <p>
                    <input id="gender" v-model="formData.gender" type="radio" value="other" checked="checked">
                    <label for="gender">Annat</label>
                 </p>
                

            
            <div id="map-wrapper">
              <div id="map" v-on:click="setLocation">
                <div
                  v-if="location.x !== 0 && location.y !== 0"
                  class="target-marker"
                  :style="{ top: location.y + 'px', left: location.x + 'px' }">
                  T
                </div>
              click here
              </div>
            </div>
          

            <button type="submit" v-on:click="submitOrder">
                <img src="/img/button.png" style="max-width: 50px;" >
                Lägg order!
            </button>
            

            </form>
          </section>
        </main>
        <hr>
        <footer>
            <p>&copy; 2024 Hedvig Ejderhamn Roupé </p>
        </footer>

</template>

<script>
import OneBurger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'
import { ref } from 'vue'

const socket = io("localhost:3000");

function MenuItem(name = "Unknown", url="", price = 0, kCal = 0, gluten = false, lactose = false) {
    this.name = name; // The *this* keyword refers to the object itself
    this.pic = url;
    this.kCal = kCal;
    this.gluten = gluten;
    this.lactose = lactose;
    this.price = price;
}

let burger1 = new MenuItem("Classic cheeseburger", "/img/cheeseburger.png", 1000, 670, true, false)
let burger2 = new MenuItem("Double cheeseburger", "/img/dubble.png", 110, 920, true, false)
let burger3 = new MenuItem("Buffalo chicken", "/img/chicken.png", 95, 640, true, true)

let burgerArray = [burger1, burger2, burger3]

export default {
  name: 'HomeView',
  components: {
    OneBurger
  },
  data: function () {
    return {
      header: "Välkommen till Hedvigs Hamburgare",
      orderedBurgers: {},
      burgers:menu,
      formData:{
        firstname: '',
        email: '',
        payment: '',
        gender: 'other',},
      location: {x:0, y:0}
    }
  },

  methods: {
    setLocation: function (event) {
        console.log(event);
        var offset = {
            x: event.currentTarget.getBoundingClientRect().left,
            y: event.currentTarget.getBoundingClientRect().top,
        };
        this.location.x = event.clientX - offset.x;
        this.location.y = event.clientY - offset.y;
        console.log("Plats uppdaterad:", this.location);
    },

    submitOrder() {
      const orderItems = Object.entries(this.orderedBurgers).map(([name, amount]) => {
        return { name, amount };
      });

      const orderData = {
        orderId: this.getOrderNumber(),
        details: { x: this.location.x, y: this.location.y },
        customerInfo: this.formData,
        orderItems: orderItems,
        };
      socket.emit("addOrder", orderData);
    },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    updateOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },

    addOrder: function (event) {
      console.log(event)
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location.x = event.clientX - offset.x;
      this.location.y = event.clientY - offset.y;

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                              }
                 );
    }
  }
}

</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

  body {
      font-family: Arial, Helvetica, sans-serif;
      padding: 0;
      margin: 0;
  }

  p {
      color: hotpink;
  }

  h1 {
      color:#050300;
      text-align: center;
      font-size: 36pt;
  }

  header {
      margin-bottom:10px;
      background-size: cover;
      width: 100%; /* 100 % av skärmens bredd */
      height: 200px;
      position: relative;
      display: flex;
      align-items: center;
  }

  header img {
      position: absolute;
      width: 100%;
      height: 100%;
      object-fit: cover;
      opacity: 0.4;

  }

  header h1 {
      position: absolute;
      width: 100%;
      color: black;
  }

  img {
      border: 1px solid #eee;
      border-radius: 3px;
  }

  section{
      margin: 20px;
      padding: 30px;
  }

  #burgers {
      background-color:black;
      color:#eee;
      border: 5px solid hotpink;
  }

  #info{
      border: 5px dotted #050300;
  }


  .burger-grid{
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap:20px;
      width: 100%;
      box-sizing: border-box;
  }

  .burger-item{
      margin: 10px; /* Utrymme mellan varje div */
      padding: 10px; /* Inre utrymme för varje div */
      border: 1px solid #f76acd; /* lägg till en tunn kant */
      border-radius: 8px; /* Gör kanterna lite rundade */
      display:flex;
      flex-direction: column;
      align-items: center;
  }

  .burger-item img {
  width: 100%; /* Gör bilden lika bred som containern */
  height: auto; /* Bevarar proportionerna */
  object-fit: contain; /* Gör så att bilden håller sig inom ramen */
  border-radius: 5px; /* Rundar hörnen (valfritt) */
}

  .ingredient { 
      font-weight: bold;
  }

  #map-wrapper{
    margin: 20px;
    width: 800px;
    height: 500px;
    overflow:scroll;
  }

  #map {
    background: url("/img/polacks.jpg");
    width:1920px;
    height: 1078px;
    position: relative; /* Gör att element inuti kan positioneras absolut */
    overflow:hidden;
  }

  .target-marker {
    position: absolute; /* Viktigt för att använda top och left */
    text-align: center;
    width: 20px;
    height: 20px;
    color: white;
    background-color: hotpink;
    border-radius: 50%; /* Gör markören cirkulär */
    transform: translate(-50%, -50%); /* Centrera kring klickpositionen */
}

  button {
      margin-left:20px;
      margin-top: 20px;
      margin-bottom: 20px;
      background-color: pink;
  }

  button:hover {
      background-color: pink;
      color:hotpink;
      outline: 3px solid hotpink; /* Lägg till en rosa kant */
      outline-offset: 1px;
      cursor: pointer;
  }

  footer{
      margin-left:20px;
  }

</style>