<template>
  <div id="app">
    <header>
      <h1>{{ sitename }}</h1>
      <button @click="showCheckout">{{ this.cart.length }}Checkout</button>
    </header>
    <main>
      <course-list @addcourse="addToCart"></course-list>
      <checkout :cart="cart"></checkout>
    </main>
  </div>
</template>

<script>
import courseList from "./components/courses.vue"
import checkout from "./components/checkout.vue"
  export default {
    name: "app",
    components: {
      courseList,
      checkout,
    },
    data() {
      return {
        sitename: "Mr-Mohs School",
        cart: []
      }
    },
    methods: {
      showCheckout() {},
      addToCart(course) {
        console.log("Add received");
        this.cart.push(course);
      },
  }
  }
</script>