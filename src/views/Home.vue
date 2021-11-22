<template>
  <section id="huvud">
    <img src="https://media.goshopping.dk/media/45563/sundere-pommes-frites.jpg?width=780&aspectratio=2.4" id="bild">
    <header>
      <h1>Välkommen till BurgerOnline</h1>
    </header>
  </section>
  <main>
    <section id="section1">
      <h2  style="color: white">Välj hamburgare</h2>
      <p  style="color: white">Här kan du välja hamburgare</p>
      <div class="wrapper">
          <Burger v-for="burger in burgers"
                  v-bind:burger="burger"
                  v-bind:key="burger.name"
                  v-on:orderedBurger="addToOrder($event)"></Burger>
      </div>
    </section>
    <section style="clear:left" id="section2">
      <h2>Kundinformation</h2>
      <p>Här anger du viktig kundinformation</p>
      <h>Leveransinformation</h>
      <form>
        <p>
          <label for="name">Fullständigt namn</label><br>
          <input type="text" id="name" v-model="name" required="required" placeholder="t.ex. Lukas Ernsund">
        </p>
        <p>
          <label for="email">E-post</label><br>
          <input type="email" id="email" v-model="em" required="required" placeholder="t.ex. hej@gmail.com">
        </p>
        <!--<p>
          <label for="street">Gatuadress</label><br>
          <input type="text" id="strees" v-model="ga" required="required" placeholder="t.ex. Trädgårdsgatan">
        </p>
        <p>
          <label for="house">Husnummer</label><br>
          <input type="number" id="house" v-model="hu" required="required" placeholder="t.ex. 17C">
        </p> -->
      </form>
      <p>
        <label for="payment">Välj betalsätt</label><br>
        <select id="payment" v-model="rcp">
          <option>Bankkort</option>
          <option>Swish</option>
          <option>Faktura</option>
          <option>Klarna</option>
        </select>
      </p>
      <h3>Ange kön</h3>
      <p>
        <input type="radio" id="gender" value="man" v-model="ge" required="required">
        <label for="gender">Man</label>
      </p>
      <p>
        <input type="radio" id="gender" value="kvinna" v-model="ge" required="required">
        <label for="gender">Kvinna</label>
      </p>
      <p>
        <input type="radio" id="gender" value="villEjAnge" v-model="ge" required="required">
        <label for="gender">Vill ej ange</label>
      </p>
      <section id="smallMap">
      <div id="map" v-on:click="setLocation" >
        <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">T</div>
      </div>
      </section>
    </section>
    <button type="submit" v-on:click="buttonClick">
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/bd/Checkmark_green.svg/1200px-Checkmark_green.svg.png" style="height: 15px;">
      Send Info
    </button>
  </main>
  <hr>
  <footer>
    <p>&copy; 2021 Lukas Hamburgare</p>
  </footer>
</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

/*
function MenuItem (name,kcal,pic,gluten,lactose) {
  this.name = name;
  this.kCal = kcal;
  this.picture = pic;
  this.gluten = gluten;
  this.lactose = lactose;
}

const burgersArray = [new MenuItem("Barger", 300,"https://assets.epicurious.com/photos/5c745a108918ee7ab68daf79/2:1/w_3756,h_1878,c_limit/Smashburger-recipe-120219.jpg", false, true),
  new MenuItem("Birger",400,"https://www.burgerdudes.se/wp-content/uploads/2021/01/moes_burger_flamin_moe_23sep21_med.jpg",true,true),
  new MenuItem("Berger",500,"https://www.max.se/globalassets/images/burgers/burgers-frisco-burger.png?width=1160&height=652",false,false)];



console.log(burgersArray)

 */

const burgersArrayNew = menu;

export default {
  name: 'Home',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: burgersArrayNew,
      name:"",
      em:"",
      ga:"",
      hu:"",
      rcp:"",
      ge:"",
      orderedBurgers:{},
      location: { x: 0,
        y: 0
      }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    },
    buttonClick: function() {
      console.log(this.name, this.em, this.ga, this.ge, this.rcp, this.orderedBurgers);
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
            details: { name:this.name,em:this.em,rcp:this.rcp,ge:this.ge,x:this.location.x,y:this.location.y },
            orderItems: [this.orderedBurgers]
          }
      );
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amountOrdered;
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top};
      this.location = { x: event.clientX - 10 - offset.x,
          y: event.clientY - 10 - offset.y }
    }
  }
}
</script>

<style>
body {
  font-family: arial;
}

.aller {
  font-weight: bold;
}

#huvud {
  height: 120px;
  overflow: hidden;
}

header {
  font-size: 30px;
  position: absolute;
  padding-left: 20px;
  margin-left: 2%;
  margin-top: -64%;
}

#section1 {
  padding: 20px;
  margin: 2%;
  background-color: black;
  color: #000000;
  border-color: black;
  border: 4px dashed #ffffff;
}

#section2 {
  padding: 20px;
  margin: 2%;
  padding: 20px;
  margin: 2%;
  border: 4px dashed #000000;
}

button:hover {
  background-color: blue;
}

a[href], input[type='submit'], input[type='image'], label[for], select, button, .pointer {
  cursor: pointer;
}


button {
  margin-left: 2%;
}

div {
  margin: 5px;
  padding: 25px;
}

#bild {
  opacity: 0.5;
  width: 100%;
  height: auto;
}

.wrapper {
  display: grid;
  grid-gap: 10px;
  grid-template-columns: 33% 33% 33%;
  color: white;
}

.box {
  color: #fff;
  border-radius: 5px;
  padding: 20px;
  font-size: 100%;
}

  #map {
    width: 1920px;
    height: 1078px;
    /*background-color: red;*/
    background: url("/img/polacks.jpg");
    position: relative;
    margin: 0;
    padding: 0;
    background: url(/img/polacks.jpg);
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
  }

  #map div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }

  #smallMap {
    width: 50%;
    height: 600px;
    overflow: scroll;
  }

</style>
