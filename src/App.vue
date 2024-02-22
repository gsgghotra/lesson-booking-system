<template>
  <nav>
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </nav>
  <router-view/>

  <div v-if='showProduct'>
    <!-- product page code -->

    <header>
        <button v-on:click='showCheckout' v-if="this.cart.length">
        {{this.cart.length}}
        <span class="fas fa-shopping-cart"></span> Checkout
    </button>
    </header>
    <!-- Sorting Options -->
    <div id="sort">
        <p>Sort by</p>
        <div v-for="(selectedValue, key) in sorting" :key="key">

            <input type="radio" id="key" name="key" value="selectedValue" v-on:click='sortBy(selectedValue)'>
            <label for="key"> {{key}} </label>
            <br>
        </div>

        <p>
            <strong>Order:</strong>
            <select v-model="orderAscending">
            <option disabled value="">Order By</option>
            <option v-for="(selectedValue, key) in userOrder" v-on:change='ss' v-bind:value="selectedValue" :key="key"/>
                {{selectedValue}}
        </select>
        </p>
    </div>

    <!-- Searching Options -->
    <div id="search">
        <input placeholder="Search..." type="text" v-model="search"> {{filteredLessons}}
    </div>

    <div class="contain">
        <div class="prod_box" v-for="(lesson, key) in lessons" :key="key">
            <!-- lesson information -->
            <h2 v-text="lesson.subject"> </h2>
            <img v-bind:src="lesson.image" height="125">
            <p v-text="lesson.location"> </p>
            <p>Price:£{{lesson.price}}</p>
            <p>Available:{{lesson.availableInventory}}</p>
            <!-- Font Awesome generated icons -->
            <div>
                <span v-for='(n, key) in lesson.rating' :key="key"><i class="fas fa-star"></i></span>
                <span v-for='(n, key) in 5-lesson.rating' :key="key" ><i class="far fa-star"></i></span>

            </div>
            <!-- Button for adding lesson to cart -->
            <button v-on:click='addToCart(lesson)' v-if='lesson.availableInventory'>
            Add to cart
            </button>
            <!-- Disable Button for adding lesson to cart when required-->
            <button disabled='disabled' v-else>
                Add to cart
            </button>
            <span v-if='lesson.availableInventory < 1'>
                All out!
            </span>
            <span v-else-if="lesson.availableInventory  < 5 ">
                Only {{lesson.availableInventory }} left!
            </span>
            <span v-else>Buy Now</span>
        </div>
    </div>
</div>
</template>
<script>
/* eslint-disable */
import checkout from './components/checkout.vue'
import lesson from './components/lesson.vue'

export default {
  name: 'App',
  components: { checkout, lesson },
  data() {
    return {
      sitename: 'BookingSystem',
      cart: [],
      currentView: true,
      lessons: [],
      orderAscending: '',
      search: '',
      ormIsNotCorrect: true,
      userSelection: '',
      OrderName: '',
      cartLessonsID: [],
      cartLessonsCounter: [],
      OrderPhone: '',
      searchResult: [],
      sorting: {
        Price: 'price',
        Subject: 'subject',
        Location: 'location',
        Availability: 'availableInventory'
      },
      userOrder: {
        a: 'Ascending',
        b: 'Descending'
      },
      showProduct: true,
            cart: [] // array to store items in shopping cart
    }
  },
  methods: {
    showCheckout() {
      this.currentView = this.currentView ? false : true;
    },
    
    submitForm() {
      for (let s = 0; s < this.lessons.length; s++) {
        let lessonID = this.lessons[s].id
        let spaces = this.cartCount(lessonID)
        if (spaces > 0) {
          this.cartLessonsID.push({ lessonID });
          this.cartLessonsCounter.push({ spaces });
        }
      }

      fetch('http://localhost:3000/collection/Order', {
        method: 'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          "LessonID": this.cartLessonsID,
          "Name": this.OrderName,
          "Phone": this.OrderPhone,
          "Spaces": this.cartLessonsCounter
        }),
      });

      for (let i = 0; i < this.cartLessonsID.length; i++) {
        for (let s = 0; s < this.lessons.length; s++) {
          let lessonID = this.lessons[s].id;
          if (lessonID === this.cartLessonsID[i].lessonID) {
            var availableSpace = this.lessons[s].availableInventory;
            var tmpLessonID = this.cartLessonsID[i].lessonID;
            fetch('http://localhost:3000/update/Lessons/' + lessonID, {
              method: 'PUT',
              headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
              },
              body: JSON.stringify({
                "availableInventory": availableSpace
              }),
            });
          }
        }
      }
      alert('Order Submitted Successfully!');
      document.location.reload(true);
    },
    canAddToCart(lessons) {
          return lessons.availableInventory > this.cartCount(lessons.id)
      },
      cartCount(id) {
          let count = 0;
          for (let i = 0; i < this.cart.length; i++) {
              if (this.cart[i].lessons.id === id)
                  count++;
          }
          return count;
      },
      sortBy(scenario) {
          if (scenario === "price") {
              function compare(a, b) {
                  if (a.price > b.price) return 1;
                  if (a.price < b.price) return -1;
                  return 0;
              }
              this.orderAscending = "Descending"
              return this.lessons.sort(compare);
          } else if (scenario === "availableInventory") {
              function compare(a, b) {
                  if (a.availableInventory > b.availableInventory) return 1;
                  if (a.availableInventory < b.availableInventory) return -1;
                  return 0;
              }
              this.orderAscending = "Descending"
              return this.lessons.sort(compare);
          } else if (scenario === "location") {
              function compare(a, b) {
                  if (a.location > b.location) return 1;
                  if (a.location < b.location) return -1;
                  return 0;
              }
              this.orderAscending = "Descending"
              return this.lessons.sort(compare);
          } else if (scenario === "subject") {
              function compare(a, b) {
                  if (a.subject > b.subject) return 1;
                  if (a.subject < b.subject) return -1;
                  return 0;
              }
              this.orderAscending = "Descending"
              return this.lessons.sort(compare);
          }
      },
      addToCart(lessons) {
      let tmpLessonID = lessons.id;
      this.cart.push({
        cartId: (this.cart.length + 1),
        lessons,
        tmpLessonID
      });
    },
  },
  computed: {
    ss() {
      if (this.orderAscending === "Ascending") {
        this.orderAscending = "Ascending";
        return this.lessons.reverse();
      } else if (this.orderAscending === "Descending") {
        this.orderAscending = "Descending";
        return this.lessons.reverse();
      } else {
        return this.lessons;
      }
    },
    sortedLessons() {
      function compare(a, b) {
        if (a.userSelection > b.userSelection) return 1;
        if (a.userSelection < b.userSelection) return -1;
        return 0;
      }
      return this.lessons.sort(compare);
    },
    filteredLessons() {
      var localApp = this;
      if (!this.search) {
        fetch('http://localhost:3000/collection/Lessons', {
          method: 'GET'
        })
        .then(function(response) {
          if (response.ok) {
            console.log('Successfully fetched the Data');
          } else {
            console.log('Error fetching data');
          }
          return response.json();
        })
        .then(function(json) {
          console.log(json);
          localApp.lessons = json;
        })
        .catch(function(error) {
          console.log(error);
        });
      } else {
        fetch('http://localhost:3000/Search/Lessons/?search=' + this.search + '', {
          method: 'GET'
        })
        .then(function(response) {
          if (response.ok) {
            console.log('Success');
          } else {
            console.log('Error fetching data');
          }
          return response.json();
        })
        .then(function(json) {
          console.log(json);
          localApp.lessons = json;
        });
      }
    }
  },
  created: function() {
    this.filteredLessons;
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

body {
    font-family: Verdana;
    font-size: 15px;
    line-height: 1.5;
    padding: 0.5%;
    margin: 0.5%;
    background-color: #f4f4f4;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
.prod_box {
    float: left;
    display: inline;
    padding: 1%;
    margin: 1%;
}

header {
    position: absolute;
    right: 50%;
}

.contain {
    width: 100%;
    float: left;
    display: inline;
    border-top: 2px solid gray;
    border-bottom: 2px solid gray;
}

#checkout {
    display: block;
    width: 200px;
}
#search li {
    list-style-type: none;
    text-align: left;
    background-color: #f6f6f6;
    /* Grey background color */
    display: block;
    /* Make it into a block element to fill the whole list */
    border-left: 2px solid #000;
    width: 165px;
}

#search {
    margin-bottom: 1%;
}

#search ul {
    padding: 0px;
}

#search {
    padding: 0px;
    width: 18%;
    height: 10%;
}

#search ul :hover {
    background-color: rgba(238, 238, 238, 0.87);
    /* Add a hover effect to all links, except for headers */
}
</style>
