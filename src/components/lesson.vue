<template>
    <div class="contain">
        <div class="prod_box" v-for="(lesson, index) in lessons" :key="'A' + index">
            <!-- lesson information -->
            <img v-bind:src="lesson.image" height="125">
            <h2 v-text="lesson.subject"> </h2>
            <p>Price: £{{lesson.price}}</p>
            <p>Campus: {{ lesson.location }}</p>
            <p> Places Available: {{lesson.availableInventory}}</p>
            <!-- Font Awesome generated icons -->
            <div>
                <span v-for='n in lesson.rating' :key="n + index"><i class="fas fa-star"></i></span>
                <span v-for='n in 5 - lesson.rating' :key="n + index + index"><i class="far fa-star"></i></span>
            </div>
            <!-- Button for adding lesson to cart -->
            <button v-on:click='addToCart(lesson)' v-if='lesson.availableInventory'>
                Add to cart
            </button>
            <!-- Disable Button for adding lesson to cart when required-->
            <button class="sold_out" disabled='disabled' v-else>
                Sold Out!
            </button>
            <div class="stock" v-if="lesson.availableInventory  < 5 ">
                Only {{lesson.availableInventory}} left!
            </div>
        </div>
    </div>
</template>

<script>
/* eslint-disable */
export default {
    name: 'Lesson',
    props: ['lessons'],
    data() {
        return {}   
    },
    methods:{
    addToCart: function(lessons) {
        function ifCanAdd(item) {
                if (item.id == lessons.id) {
                    lessons.availableInventory -= 1;
                    return item;
                }
            }
            this.lessons.find(ifCanAdd);
            this.$emit('addProduct', lessons)
        }
    }
}
</script>
