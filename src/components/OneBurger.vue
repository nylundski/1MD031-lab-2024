<template>
  <div style="display: inline-block" class="burger">
    
    <h4>{{ burger.name }}</h4>
    <img
      v-bind:src="burger.URL"
      v-bind:alt="burger.name"
      v-bind:title="burger.name"
      style="width: 300px"
    />
    <ul>
      <li>{{ burger.description }}</li>
      <li v-if="burger.gluten" class="allergenic">Contains gluten</li>
      <li v-else>Gluten-free</li>
      <li v-if="burger.lactose" class="allergenic">Contains lactose</li>
      <li v-else>Lactose-free</li>
      <li>{{ burger.kCal }} kcal</li>
    </ul>
    <div class="order-controls">
        <button v-on:click="decreaseAmount">-</button>
        <span>{{ amountOrdered }}</span>
        <button v-on:click="increaseAmount">+</button>
      </div>
   
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0, 
    };
  },
  methods: {
    decreaseAmount() {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit("updateOrder", { burger: this.burger, amount: this.amountOrdered})
      }

    },
    increaseAmount() {
      this.amountOrdered++;
      this.$emit("updateOrder", {burger: this.burger, amount: this.amountOrdered});
    }
  }
  
};

</script>

<style scoped>

.burger {
    
    padding: 10px;
    text-align: center;
    align-items: center;
    border: 1px solid #ddd;
    border-radius: 8px;
    width: 400px;
    height: 500px;
    box-sizing: border-box;

}

.burger img {
  max-width: 100%;
  height: auto;

}

.allergenic {
  font-weight: bold;
}

#burgerchoice {
  background-color: #000;
  color: #fff;
  border-style: dashed;
  border-width: 5px;
  border-color: #fff;
}
</style>