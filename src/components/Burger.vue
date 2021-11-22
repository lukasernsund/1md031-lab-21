<template>
  <div>
  <h3 v-bind:key=burger.name>{{ burger.name }}</h3>
  <img v-bind:src="burger.picture" alt="Hamburger 1" style="width: 300px;height:200px">
  <ul>
    <li burger.kCal>{{ burger.kCal }} kCal</li>
    <li>Extremt god</li>
    <li class="aller" v-if="burger.lactose">Kan innehålla spår av <span id="allergy">laktos</span></li>
    <li class="aller" v-if="burger.gluten">Kan innehålla spår av <span id="allergy">gluten</span></li>
  </ul>
    <button type="button" v-on:click="decreaseAmount" >-</button>
      {{ amountOrdered }}
    <button type="button" v-on:click="increaseAmount">+</button>
  </div>
</template>

<script>
export default {
  name: 'Burger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    decreaseAmount: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit('orderedBurger', { name:   this.burger.name,
          amountOrdered: this.amountOrdered
        }
        );
      }
    },
  increaseAmount: function () {
    this.amountOrdered++;
    this.$emit('orderedBurger', { name:   this.burger.name,
          amountOrdered: this.amountOrdered
        }
    );
  }
  }
}



</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.gluten {
  font-weight: bold;
}

.lactose{
  font-weight: bold;
}

</style>
