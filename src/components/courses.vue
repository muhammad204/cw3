<template>
    <div>
        <div v-for="course in courses" :key="course.id">
            <h2>{{ course.title }}</h2>
            <figure>
                <img v-bind:src="course.img"/>
            </figure>
            <p v-html="course.description"></p>
            <p>Price: {{ course.price }}</p>
            <p>Available Stock: {{ course.availableInventory }}</p>
            <button @click="add(product)">Add to cart</button>
        </div>
    </div>
</template>

<script>
    export default {
  name: "courseList",
  data() {
    return {
      sitename: "Welcome to After School!",
      courses: [],
      searchQuery: "",
      cart: [],
      showCourse: true,
      sortOption: "title",
      sortOrder: "asc",
      order: {
        firstName: "",
        phoneNumber: ""
      }
    };
  },

  created() {
    this.getCourses();
  },

  methods: {
    // method to get lessons from mongo db
    async getCourses() {
      var url = "https://cw-2-smoky.vercel.app/collection/courses/";
      var courses = await fetch(url);
      courses = await courses.json();
      this.courses = courses;
      console.log("fetched");
    },

    async searchCourses() {
      var query = {
        search: this.searchQuery,
        sort: this.sortOption,
        order: this.sortOrder
      };
      var courses = await fetch(
        "https://cw-2-smoky.vercel.app/collection/courses/",
        {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(query)
        }
      );
      courses = await courses.json();
      this.courses = courses;
      console.log("fetched");
    },

    addToCart(course) {
      this.cart.push(course.id);
    },

    removeFromCart(courseID) {
      const index = this.cart.indexOf(courseID);
      if (index !== -1) {
        this.cart.splice(index, 1);
      }
    },

    showCheckout() {
      this.showCourse = !this.showCourse;
    },

    submitForm() {
      alert("Order submitted!");
    },

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

    async checkout() {
      var order = {
        // all cart items
        courses: [...this.cart],
        firstName: this.order.firstName,
        phoneNumber: this.order.phoneNumber
      };

      this.cart.forEach((item) => {
        item = this.courses.filter((course) => course.id == item)[0];
        item.availableInventory -= this.cartCount(item.id);
        fetch(`https://cw-2-smoky.vercel.app/collection/courses/${item._id}`, {
          method: "PUT",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            availableInventory: item.availableInventory
          })
        });
      });

      await fetch("https://cw-2-smoky.vercel.app/collection/orders/", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(order)
      });

      alert("order successful");
      this.cart = [];
      this.showCourse = true;
    }
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

    validInput() {
      return (
        /^[a-zA-Z\s]+$/.test(this.order.firstName) &&
        /^\d+$/.test(this.order.phoneNumber)
      );
    }
  }
};
    </script>
