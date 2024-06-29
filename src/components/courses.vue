<template>
  <div>
    <button
      v-bind:disabled="cart.length == 0"
      v-on:click="showCheckout"
      class="btn btn-outline-success btn-lg float-end m-4 p-2"
    >
      Checkout {{ cartItemCount }}</button
    ><br />

    <div class="row m-3">
      <main>
        <div v-if="showCourse">
          <div class="form-group col-md-6 m-2">
            <label for="">Sort by</label>
            <select
              v-model="sortOption"
              class="form-control"
              style="width: 350px"
            >
              <option value="title">Title</option>
              <option value="location">Location</option>
              <option value="price">Price</option>
              <option value="spaces">Spaces</option>
            </select>
            <br />
            <select
              v-model="sortOrder"
              class="form-control m-2"
              style="width: 350px"
            >
              <option value="asc">Ascending</option>
              <option value="desc">Descending</option>
            </select>
          </div>

          <div class="col-md-6 m-2">
            <input
              class="form-control"
              type="text"
              v-model="searchQuery"
              placeholder="Search lessons..."
            />
          </div>

          <course-list
            :is="currentComponent"
            @addcourse="addToCart"
            @canCart="canAddToCart"
            @cartcounter="cartCount"
          ></course-list>
        </div>

        <div v-else>
          <checkout
            :cart="cart"
            @cartCount="cartCount"
            @removeItem="removeFromCart"
            @submitForm="submitForm"
          ></checkout>
        </div>
      </main>
    </div>
  </div>
  <div class="row m-4">
    <div class="col-md-4" v-for="course in sortedCourses" :key="course.id">
      <div class="card md-4 m-2 shadow">
        <img
          v-bind:src="'https://cw-2-smoky.vercel.app/' + course.image"
          class="ct height: 300px; width: 100%; mx-auto d-block img-fluid"
        />

        <div class="card-body">
          <h2 v-text="course.title"></h2>
          <p v-html="course.description"></p>
          <p>Location: {{ course.location }}</p>
          <p>Price: {{ course.price }}</p>

          <p>
            Available stock:
            {{ course.availableInventory - cartCount(course.id) }}
          </p>

          <button
            v-on:click="addItem(course)"
            v-if="canAddToCart(course)"
            class="btn btn-outline-info"
          >
            Add to cart
          </button>
          <button disabled="disabled" v-else>Add to cart</button>
          <span v-if="course.availableInventory === cartCount(course.id)">
            All out!
          </span>

          <span
            v-else-if="course.availableInventory - cartCount(course.id) < 5"
          >
            Only {{ course.availableInventory - cartCount(course.id) }}
            left!
          </span>
          <span v-else>Buy now!</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import courseList from "./components/courses.vue";
import checkout from "./components/checkout.vue";
export default {
  name: "courseList",
  props: ["addcourse", "canCart", "sortoption"],
  data() {
    return {
      sitename: "Welcome to After School!",
      courses: [],
      searchQuery: "",
      cart: [],
      showCourse: true,
      sortOption: "title",
      sortOrder: "asc",
    };
  },
  components: {
    courseList,
    checkout,
  },

  created() {
    this.getCourses();
  },

  methods: {
    async getCourses() {
      var url = "https://cw-2-smoky.vercel.app/collection/courses/";
      var courses = await fetch(url);
      courses = await courses.json();
      this.courses = courses;
      console.log("fetched");
    },
    addItem(course) {
      // Emit an event to notify the parent component to remove the item
      this.$emit("addcourse", course);
    },
    // addToCart(course) {
    //   this.cart.push(course.id);
    // },
    // canAddToCart(course) {
    //   this.$emit('canCart', course);
    // },
    // cartCount(id) {
    //   this.$emit('cartCounter', id)
    // }
    canAddToCart(course) {
      return course.availableInventory > this.cartCount(course.id);
    },

    cartCount(id) {
      let count = 0;
      for (let i = 0; i < this.cart.length; i++) {
        if (this.cart[i] === id) {
          count++;
        }
      }
      return count;
    },
  },

  computed: {
    cartItemCount() {
      return this.cart.length;
    },
    cartCourses() {
      const selectedCourses = {};

      this.cart.forEach((id) => {
        if (selectedCourses[id]) {
          selectedCourses[id].count++;
        } else {
          const course = this.courses.find((c) => c.id === id);
          if (course) {
            selectedCourses[id] = { course, count: 1 };
          }
        }
      });

      return Object.values(selectedCourses);
    },
    sortedCourses() {
      var coursesArray = this.courses;
      var order = this.sortOrder === "asc" ? 1 : -1;

      if (this.searchQuery) {
        var query = this.searchQuery.toLowerCase();
        coursesArray = coursesArray.filter(
          (c) =>
            c.title.toLowerCase().includes(query) ||
            c.location.toLowerCase().includes(query)
        );
      }
      return coursesArray.sort((a, b) => {
        if (a[this.sortOption] > b[this.sortOption]) return 1 * order;
        if (a[this.sortOption] < b[this.sortOption]) return -1 * order;
        return 0;
      });
    },
  },
};
</script>
