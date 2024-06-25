<template>
    <div>
      <h2>Checkout</h2>
      <h3>Added course</h3>
      <div v-for="(course, index) in cart" :key="course.id">
        {{ product.id }}
        <button @click="removeItem(index)">remove</button>
      </div>
      <div>
        <strong>Name </strong>
        <input v-model="name" />
        <strong> Address </strong>
        <input v-model="address" />
        <button @click="submitForm">Submit</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "Form-Component",
    props: ['cart'],
    data() {
      return {
        name: "",
        address: "",
      };
    },
    methods: {
      removeItem(index) {
        // Emit an event to notify the parent component to remove the item
        this.$emit('remove-item', index);
      },
      submitForm() {
        // Here you can emit an event or call an API to submit the form
        // For example:
        // this.$emit('submit-form', { name: this.name, address: this.address });
        
        console.log("Form submitted with:", this.name, this.address);
        // Reset form fields after submission
        this.name = "";
        this.address = "";
      }
    },
  };
  </script>