<template>
    <div>
        <!-- checkout page code -->
        <button v-on:click='backButton'>
            <span class="fas fa-arrow-left"></span> Go Back
        </button>
        <!-- Basket code to display products in basket -->
        <h2> Your Basket </h2>
        <div class="contain">
            <div class="prod_box" v-for="(item, index) in cart" :key="index">
                <p v-text="item.lessons.subject"> </p>
                <img v-bind:src="item.lessons.image" height="50">
                <p v-text="item.lessons.location"> </p>
                <p>Price: {{item.lessons.price}}</p>
                <button v-on:click="deleteFromCart(item)">
                    remove
                </button>
            </div>
        </div>
        <!-- Display checkout inputs for user -->
        <div id="checkout">
            <h2> Checkout</h2>
            <form action="#" method="GET" onchange="return checkData()" name="signup">
                <br /><br />Name:
                <input name="NAME" type="text" id="NAME" v-model="OrderName" />
                <br /><br /> Phone Number:
                <input name="PHONE" type="text" id="PHONE" v-model="OrderPhone" />
                <input @click="submitForm()" v-bind:disabled='formIsNotCorrect' id="Button" type="submit" value="Place Order">
            </form>
        </div>
    </div>
</template>
<script>
/* eslint-disable */
export default {
    name: 'checkout',
    props: ['cart'],
    data() {
        return {
            OrderName: '',
            OrderPhone: '',
            formIsNotCorrect: true
        }   
    },
    methods : {
        deleteFromCart(item) {
            this.$emit('deleteFromCart', item)
        },
        backButton() {
            this.$emit('showCheckout')
        },
        submitForm() {
            this.$emit('submitForm')
        }
    }
}
    function checkData() {
        //regex expression:
        var phoneRegex = /^[0-9]*$/;
        var nameRegex = /^[a-zA-Z]*$/;
        var name = document.signup.NAME.value;
        var phone = document.signup.PHONE.value;


        var phoneResult = phoneRegex.test(phone);
        var nameResult = nameRegex.test(name);


        if (!phoneResult) {
            alert("Please fill in your Phone Number with numbers only.")
            document.signup.PHONE.focus()
            document.getElementById("Button").disabled = true;
            return false;
        }
        if (!nameResult) {
            alert("Please fill in your Name with letters only.")
            document.signup.NAME.focus()
            document.getElementById("Button").disabled = true;
            return false;
        }

        if (document.signup.NAME.value == "") {
            alert("Please fill in your name.")
            document.signup.NAME.focus()
            return false;
        }
        if (document.signup.NAME.value.length < 5) {
            alert("Please fill in your name with at least 5 letters.")
            document.signup.NAME.focus()
            return false;
        }
        if (document.signup.PHONE.value == "") {
            alert("Please fill in your PHONE.")
            document.signup.PHONE.focus()
            return false;
        }
        if (document.signup.PHONE.value.length < 5) {
            alert("Please fill in your phone with at least 11 numbers.")
            document.signup.PHONE.focus()
            return false;
        } else {
            document.getElementById("Button").disabled = false;
            return true;
        }
    }
</script>