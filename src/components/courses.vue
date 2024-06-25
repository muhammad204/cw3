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
    export default{
        name: "courseList",
        data(){
            
        }
    }
</script>