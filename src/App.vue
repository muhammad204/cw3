<template>
  <div id="app">
    <header>
      <h1>{{ sitename }}</h1>
      <button @click="showCheckout">{{ this.cart.length }}Checkout</button>
    </header>
  </div>
</template>

<script>
  export default {
    name: "app",
    data() {
      return {
        sitename: "Mr-Mohs School",
        cart: []
      }
    },
    methods: {showCheckout() {}}
  }
</script>