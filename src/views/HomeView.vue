<template>

<div>
    
</div>
<body>
    <header id="head">
        <img src="https://img.freepik.com/premium-vector/burger-latin-american-food-pattern-vector_648861-310.jpg" alt="headpic" title="Header pic" id="headpicture">
        <h1 id="headtext" >Welcome to BooorgirOnline!</h1>
        <p id="headundertext">The bestest burgers in the world!</p>
    </header>
    <main>
        <section id="burgerchoice">
            <div id="burgerchoicetext">
            <h3>Choose your booorgir!</h3>
            <p>You can choose between these options:</p>
            </div>
            <div class="burgerwrapper">
            <Burger
            v-for="burger in burgers"
            :key="burger.name"
            :burger="burger"
            @updateOrder = "updateOrder"
            />
            </div>
        </section>
        <section id="orderinfo">
            <h3>Customer information</h3>
            <p>To be able to deliver your booorgir to you, we need the following information!</p>
            <h4>Delivery information:</h4>
            <form @submit.prevent="submitForm">
              <div class="form-group">
                  <div><label for="fullname">Full name:</label></div>
                  <input id="fullname" v-model="nm" placeholder="First- and last name" /><br>

                  <div><label for="email">E-mail:</label></div>
                  <input id="email" v-model="mail" placeholder="E-mail adress" /><br>

                  <div><label for="paymnt">Payment method:</label></div>
                    <select id="paymnt" v-model="selected" placeholder="Please select one">
                      <option disabled value="">Please select one</option>
                        <option>Visa</option>
                        <option>Mastercard</option>
                        <option>American Express</option>
                        <option>Klarna</option>
                        <option>MousePay</option>
                        <option>Swish</option>
                    </select><br>
                    <label for="map-container">Click on your point of delivery:</label>
                    <div class="map-container">
                    <div id="map" v-on:click="setLocation">
                      <div 
                      v-if="location.x !==0 || location.y !==0" 
                      style="position: absolute; color: red; font-weight: bold;" 
                      v-bind:style="{left: location.x + 'px', top: location.y + 'px'}">
                      T </div>
                    </div>
                    </div>
              
                      <div><label for="gender">Gender:</label></div>
	                    <input type="radio" id="male" value="Male" v-model="picked" name="gender" />
	                    <label for="male">Male</label>
	                    <input type="radio" id="female" value="Female" v-model="picked" name="gender" />
                      <label for="female">Female</label>
                      <input type="radio" id="other" value="Other" v-model="picked" name="gender" />
                      <label for="other">Other</label>                    
              </div>
            </form>
        </section>
        <button type="submit" v-on:click="submitForm">
            <img src="https://thumbs.dreamstime.com/b/italian-chef-smiling-pizza-hand-51938716.jpg" alt="Chef" title="Chef" style="width: 20px">
            Order your booorgir!
          </button>
    </main>
    <hr>
    <footer>
        <h4>Contact information:</h4>
        <p>contact@booorgir.com</p>
        <p>070-555-55-55</p>
        <p>&copy; The Booorgir Corporation 2024</p>
    </footer>
        
    </body>
</template>

<script>
import menu from '../assets/menu.json' 
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io("localhost:3000");

function MenuItem(name, kcal, url, glu, lac, desc) {
  this.name = name;
  this.kCal = kcal;
  this.URL = url;
  this.gluten = glu;
  this.lactose = lac
  this.description = desc
}

/*const burgerArray = [
  new MenuItem("The Nasty Patty", 500, "https://i.ytimg.com/vi/e03PjaSc7VQ/maxresdefault.jpg", true, false, "Our most delicious vegan burger"), 
  new MenuItem("Semmel burger", 450, "https://www.livetsgoda.se/wp-content/uploads/2023/02/Burger-king.jpg", true, true, "Frankly the best dessert, maybe of all time, that's what they say"),
  new MenuItem("Wagyu burger", 600, "https://i.redd.it/2d0iy7uc6ihd1.jpeg", true, true, "Our most luxurious burger")
] */

const burgerArray = menu;

console.log(burgerArray);

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      nm: "",
      mail: "",
      picked: "",
      selected: "Other",
      orderPlacedMessage: "",
      location: {x: 0, y:0},

      orderedBurgers: {}
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    setLocation: function(event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left, y: event.currentTarget.getBoundingClientRect().top};
      
      this.location = {x: event.clientX - offset.x, y: event.clientY - offset.y};

    },

    updateOrder({burger, amount}) {
      if (amount > 0) {
        this.orderedBurgers[burger.name] = amount;
      } else {
        delete this.orderedBurgers[burger.name];
      }

    },
    orderBurger: function() {
      if (!this.orderedBurgers[burger.name]){
        this.orderedBurgers[burger.name] = 1;
      } else {
        this.orderedBurgers[burger.name]++;
      }
      

      console.log(this.orderedBurgers);

    },
  
    submitForm: function(){
      if (!this.nm || !this.mail || !this.selected || !this.picked) {
      alert("Fill out the entire form before placing your order!");
      return; 
    }
      if (!this.location.x && !this.location.y) {
      alert("Please select a location on the map for your delivery!");
      return;
    }
      socket.emit("addOrder", { 
        orderId: this.getOrderNumber(),
        details: {x: this.location.x, y: this.location.y
    },
      orderItems: this.orderedBurgers,
        customerInfo: {
          name: this.nm,
          email: this.mail,
          paymentMethod: this.selected,
          gender: this.picked
    }
  });

      console.log("Full Name:", this.nm);
      console.log("Email:", this.mail);
      console.log("Payment Method:", this.selected);
      console.log("Gender:", this.picked);
      console.log(this.location)
      console.log(this.orderedBurgers)

      alert(`Thank you for placing your order ${this.nm}!`);

      this.nm = "";
      this.mail = "";
      this.picked = "";
      this.selected = "Other";
      this.location = {x: 0, y: 0}
      this.orderedBurgers = {}
      this.burgers.forEach((burger) => {
        burger.amountOrdered = 0;
    })
  }
}
}

</script>

<style>
  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    overflow: scroll;
    background-size: cover;
    position: relative;
  }

  #map:hover {
    cursor: pointer;
  }

.map-container {
  width: 800px;
  height: 600px;
  border: 2px solid #000;
  overflow: auto;
}
  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-size: 14pt;
    font-family: 'Times New Roman', Times, serif;
}

p {
    color: rgb(220, 138, 6);
}

h1 {
    font-family: 'Agbalumo';
    font-size: 36pt;
}
main, header, footer, nav ul {
    
    margin: 0 auto 0 auto;
}
main {
    background-color: white;
}

header {
    position: relative;
    background-size: cover;
    overflow: hidden;
    width: 100%;
    height: 200px;
    opacity: 0.9;
}

header h1 {
    width:40rem;
    margin: 0 auto;
    text-align: center;
}

nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}

.allergenic {
    font-weight: bold;
}

#burgerchoice {
    background-color: #000;
    color: #fff;
    border-style: dashed;
    border-width: 5px;
    border-color: #fff

}

#orderinfo {
    background-color: antiquewhite;
    border-style: dashed;
    border-width: 5px;
    border-color: black;

}

button {
    padding: 10px;
    margin: 10px;
}

button:hover {
    background-color: beige;
    cursor: pointer;
}

section {
    margin: 0 auto;
    padding: 20px;

}

.burgerwrapper {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); 
  gap: 20px; 
  justify-content: center; 
  align-items: center;
  margin: 0 auto; 
  
  min-height: 100vh; 
  box-sizing: border-box; 
  

}


#head {
    margin: 0 auto;
    border:#000;
    border-width: 5px;
    height: 15rem;
    overflow: hidden;
}

#headpicture {
    opacity: 0.6;
    width: 100%;
    height: auto;
    z-index: 1;
}

#headtext {
    position: absolute;
    top: 50%;
    left:50%;
    text-align: center;
    transform: translate(-50%, -50%); 
    color: white; 
    font-size: 50px; 
    font-family: 'Cormorant', serif; 
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.7); 
    z-index: 10;

}

#headundertext {
    position: absolute;
    text-align: center;
    z-index: 11;
    top: 70%;
    left: 37.5%;
    color:#000;
    font-size: 25px;

}

#burgerchoicetext {
    text-align: center;
}

br {
  display: block;
  margin: 10px;
}
</style>

