<template>
  <ClientOnly>
  <div class="container">
    <div>
      <Logo />
      <h1>Wapes</h1>
      <div id="app">
        <form>
          <div class="row">
            <div class="col">
              <div class="row">
                <label for="total">Total (ml)</label>
                <input id="total" type="number" v-model.number="totalVolume" v-on:keyup="calcTotal(true)">
              </div>
              <div class="row">
                <p>&nbsp;</p>
                <p>or</p>
              </div>
              <div class="row">
                <label for="base"> Base (ml)</label>
                <input id="base" type="number" v-model.number="baseVolume" v-on:keyup="calcTotal(false)">
              </div>
            </div>
          </div>
          <div class="row">
            <div class="col">
              <div class="row">
                <label for="flavorPercent">Flavor (%)</label>
                <input id="flavorPercent" type="number" max="100" v-model.number="flavorPercent" v-on:keyup="calcFlavor(true)">
              </div>
              <div class="row">
                <p>&nbsp;</p>
                <p>or</p>
              </div>
              <div class="row">
                <label for="flavorVolume">Flavor (ml)</label>
                <input id="flavorVolume" type="number" v-model.number="flavorVolume" v-on:keyup="calcFlavor(false)">
              </div>
            </div>
          </div>
          <div class="row">
            <span>Nico Strength (mg/ml)</span>
            <div class="colnico">
              <div class="nicodiv" v-for="nico in nicos" v-bind:class="{activediv:isActive(nico.rate)}" v-bind:key="nico.id">
                <a class="nicolink" v-bind:class="{activelink:isActive(nico.rate)}" v-bind:key="nico.id" v-on:click="calcRate(nico.rate)" >{{nico.rate}}</a>
              </div>
            </div>
          </div>
          <div class="row">
            <label for="flavcond">Flavor Conditionning (ml)</label>
            <input id="flavcond" type="number" v-model.number="flavorCond" v-on:keyup="calc">
          </div>
          <div class="row">
            <p>
              <span class="result">{{nbBooster}}</span>  booster(s) of 10ml for
              <span class="result">{{boostVolume}}</span> ml
            </p>
            <p>
              <span class="result">{{nbFlavor}}</span>  flavor(s) of {{flavorCond}} ml for
              <span class="result">{{flavorVolume}}</span> ml
            </p>
          </div>
          <div class="row">
            <button class="button" type="button" v-on:click="reset()">Reset</button>
          </div>
        </form>
      </div>
    </div>
    <div>
      <AppFooter/>
    </div>
  </div>
</ClientOnly>
</template>

<script setup lang="ts">
  // local ref
  const totalVolume = ref<number>()
  const baseVolume = ref<number>()
  const flavorPercent = ref<number>()
  const flavorVolume = ref<number>()
  const nicoStrength = ref(0)
  const nbBooster = ref<number>()
  const boostVolume = ref<number>()
  const flavorCond = ref<number>()
  const nbFlavor = ref<number>()
  const isTotal = ref(false)
  const isPercent = ref(true)
  const nicos = [
      {id:0, rate:0},
      {id:1, rate:3},
      {id:2, rate:6},
      {id:3, rate:9},
      {id:4, rate:12},
      {id:5, rate:16}
    ]

    //methods
    const isActive = (nico:number) => {
      if(nico==nicoStrength.value) return true;
      else return false;
    }

    const calculFlavor = () => {
      if(isPercent.value==true) {
        if(totalVolume.value!=undefined && flavorPercent.value!=undefined) {
          flavorVolume.value = Math.round(totalVolume.value * flavorPercent.value/100);
        }
      } else {
        if(totalVolume.value!=undefined && flavorVolume.value!=undefined) {
           flavorPercent.value = Math.round (flavorVolume.value/totalVolume.value*100);
        }
      }
    }

    const calcWithBase = () => {
      if(baseVolume.value != undefined) {
        totalVolume.value = baseVolume.value
        if(flavorPercent.value!=undefined) {
          if(flavorPercent.value!= undefined) {
            totalVolume.value = Math.round(baseVolume.value / (1- (flavorPercent.value/100) - (nicoStrength.value/20)));
          }
        } 
        if(flavorVolume.value!=undefined) {
          if(flavorVolume.value!= undefined) {
            totalVolume.value = Math.round((baseVolume.value + flavorVolume.value) / (1- (nicoStrength.value/20)));
          }
        }
        if(flavorPercent.value==undefined && flavorVolume.value==undefined) {
          totalVolume.value = Math.round((baseVolume.value) / (1- (nicoStrength.value/20)));
        }

        calculFlavor();
        
        if (nicoStrength.value!=undefined) {
          boostVolume.value = Math.round(totalVolume.value * nicoStrength.value / 20);
          nbBooster.value = Math.ceil(boostVolume.value/10);
        }
      }
    }

    const calcWithTotal = () => {
      baseVolume.value = totalVolume.value;

      calculFlavor();
      if(baseVolume.value!=undefined && flavorVolume.value!= undefined) {
        baseVolume.value = baseVolume.value - flavorVolume.value;
      }

      if (nicoStrength.value!=undefined && totalVolume.value!=undefined && baseVolume.value!=undefined) {
        boostVolume.value = Math.round(totalVolume.value * nicoStrength.value / 20);
        nbBooster.value = Math.ceil(boostVolume.value/10);
        baseVolume.value = Math.ceil(baseVolume.value - boostVolume.value);
      }
    }

    const calc = () => {
      nbBooster.value = 0;
      nbFlavor.value = 0;
      if(isTotal.value==true) {
        calcWithTotal ();
      } else {
        calcWithBase ();
      }
      if(flavorVolume.value!=undefined && flavorCond.value!=undefined && isFinite(flavorVolume.value/flavorCond.value)) {
        nbFlavor.value = Math.ceil(flavorVolume.value/flavorCond.value);
      }
    }

    const calcTotal = (isNewTotal:boolean) => {
      isTotal.value = isNewTotal;
      calc();
    }

    const calcFlavor = (isNewPercent:boolean) => {
      isPercent.value = isNewPercent;
      calc();
    }

    const calcRate = (rate:number) => {
      nicoStrength.value=0;
      if(rate!=null) {
        nicoStrength.value = rate;
      }
      calc();
    }

    const reset = () => {
      totalVolume.value = undefined
      baseVolume.value = undefined
      flavorPercent.value = undefined
      flavorVolume.value = undefined
      nicoStrength.value = 0
      nbBooster.value = undefined
      boostVolume.value = undefined
      flavorCond.value = undefined
      nbFlavor.value = undefined
      isTotal.value = false
      isPercent.value = true
    }

</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  background-color:#3986c4;
}

.row {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 1px;
}

.col {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-around;
}

p, label, span {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 1.5rem;
  color:white;
  font-weight: 300;
}

.result {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-weight: bold;
  font-size: 1.5rem;
}

input {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  background-color: #3986c4;
  border: 1px solid;
  font-size: 1.3rem;
  width: 90%;
  color: white;
  text-align: center;
}

.col input {
  width: 80%;
}

.colnico {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  width: 90%;
  padding: 1px;
}

.nicolink {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 1.3rem;
  color: #3986c4;
  text-decoration: none !important;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;  cursor: pointer;
}

.nicodiv {
  height: 2.5rem;
  width: 2.5rem;
  border-radius: 1.25rem;
  background-color:white;
}

.nicodiv:hover {
  background: #3986c4;
}
.nicolink:hover {
  color: white;
}

.activediv {
  background: #3986c4;
  border: solid 1px white;
}
.activelink {
  color: white;
}

input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    margin: 0; 
}

h1 {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 3rem;
  color:white;
  letter-spacing: 1px;
}

h4 {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 1.5rem;
  color:white;
  letter-spacing: 1px;
}
.button {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 1rem;
  color: #3986c4;
  background-color: white;
  border: solid 1px white;
  text-align: center;
  display: inline-block;
  border-radius: 8px;
  justify-content: center;  cursor: pointer;
}
.button:hover {
  color: white;
  background-color: #3986c4;
}

</style>
