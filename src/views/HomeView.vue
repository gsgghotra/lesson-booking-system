<template>
  <div class="home">
    <Lesson msg="Welcome to Your Vue.js App"/>
    <div v-if='showProduct'>
        <!-- product page code -->

        <header>
          <button v-on:click='showCheckout' v-if="this.cart.length">
            {{this.cart.length}}
            <span class="fas fa-shopping-cart"></span> Checkout
          </button>
        </header>

        <div class="filter_section">
                    <!-- Searching Options -->
          <div id="search">
              <input placeholder="Search.." type="text" v-model="search" class="searchBar"> {{filteredLessons}}
          </div>
          <!-- Sorting Options -->
          <div id="sort">
              <p>Sort by</p>
              <div class="sort_option" v-for="(selectedValue, key) in sorting" :key="key">

                  <input type="radio" id="key" name="key" value="selectedValue" v-on:click='sortBy(selectedValue)'>
                  <label for="key"> {{key}} </label>
                  <br>
              </div>

              <p>
                  <strong>Order: </strong>
                  <select v-model="orderAscending" class="dropdown">
                    <option disabled value="">Order By</option>
                    <option v-for="(selectedValue, key) in userOrder" v-on:change='ss' v-bind:value="selectedValue" :key="key">
                        {{selectedValue}}
                    </option>
                </select>
              </p>
          </div>
        </div>
        <Lesson  :lessons="lessons"  @addProduct='addToCart'/>
    </div>
    <div v-else>
      <!-- checkout page code -->
      <!-- Button appears in checkout page to go back to lessons page -->
      <button v-on:click='showCheckout'>
          <span  class="fas fa-arrow-left"></span> Go Back
      </button>

      <!-- Basket code to diplay products in basket-->
      <h2> Your Basket </h2>
      <div class="contain">
          <div class="prod_box" v-for="(item, key) in cart" :key="key">
              <p v-text="item.lessons.subject"> </p>
              <img v-bind:src="item.lessons.image" height="50">
              <p v-text="item.lessons.location"> </p>
              <p>Price: {{item.lessons.price}}</p>
              <button v-on:click="deleteFromCart(item)">
              Remove
          </button>
          </div>
      </div>

      <!--diplay checkout inputs for user-->
      <div id="checkout">
          <h2> Checkout</h2>

          <form action="#" method="GET" onchange="return checkData()" name="signup" class="signup">
              <br /><br />Name:
              <input name="NAME" type="text" id="NAME" v-model="OrderName" />
              <br /><br /> Phone Number:
              <input name="PHONE" type="text" id="PHONE" v-model="OrderPhone" />
              <input @click="submitForm()" v-bind:disabled='formIsNotCorrect' id="Button" type="submit" value="Place Order"  class="submit"/>
          </form>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
/* eslint-disable */
import Lesson from '@/components/lesson.vue'

export default {
  name: 'HomeView',
  components: {
    Lesson
  },
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
      this.showProduct = this.showProduct ? false : true;
    },
    addToCart(lessons) {
      let tmpLessonID = lessons.id;
      this.cart.push({
        cartId: (this.cart.length + 1),
        lessons,
        tmpLessonID
      });
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
