
<template>
  <div class="burger-item">
    <h4>{{ burger.name }}</h4>
    <img v-bind:src="burger.pic" style="max-width: 300px;">
    <ul style="max-width:260px">
      <li>{{ burger.kCal }} kcal</li>
      <li>{{ burger.price }} kr</li>
      <li v-if="burger.gluten">
        Innehåller <span class="ingredient">gluten</span>
      </li>
      <li v-if="burger.lactose">
        Innehåller <span class="ingredient">laktos</span>
      </li>
    </ul>
    <div>
      <button v-on:click="decreaseOrder">-</button>
      <button v-on:click="increaseOrder">+</button>
      <p>Antal beställda: {{ amountOrdered }}</p>
    </div>

  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger:{
      type: Object,
      required: true, /* Detta gör att OneBurger.vue vet att den kommer att få ett objekt från sin förälder */ 
    }
  },

  data: function () {
  return {
    amountOrdered: 0,
    orderedBurgers: {}
    }
  },

  methods:{
    increaseOrder() {
      this.amountOrdered++;
      this.$emit('orderedBurger', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                              }
    );
    },

    decreaseOrder() {
      this.amountOrdered = Math.max(0, this.amountOrdered - 1);
    }
  },

   // Lägger till eller uppdaterar den specifika burgaren i orderedBurgers
  updateOrderedBurgers() {
      if (this.amountOrdered > 0) {
        this.$set(this.orderedBurgers, this.burger.name, this.amountOrdered); 
      } else {
        this.$delete(this.orderedBurgers, this.burger.name);  // Ta bort burgaren om antalet är 0
      }
    }
  }



</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .ingredient { 
      font-weight: bold;
  }

  .burger-item img {
    display: block; /* Ta bort inline-egenskaper */
    margin: 0 auto; /* Centrerar bilden */
    max-width: 100%; /* Gör bilden responsiv */
    height: auto; /* Bevarar proportioner */
  }

</style>