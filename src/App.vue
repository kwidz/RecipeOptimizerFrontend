<template>
  <div id="app" class="container-fluid">
    <div class="row paramRow" >
<div class="col-11"></div>
      <div class="col align-self-right" >
        <div class="btnParam" style="font-weight:bold;color:grey;font-size:30px;">
          <font-awesome-icon icon="cogs" v-b-modal.modalParam />
        </div>
        <b-modal id="modalParam" title="Bootstrap-Vue">
          <p class="my-4">Paramètrage de votre semaine</p>
          <label>Choisissez le nombre de type de repas que vous souhaitez dans votre semaine</label>
          <div class="row"><div class="col text-align-left">Poisson</div><div class="col"><input type="number" name="quantity" min="0" max="5"></div></div>
          <div class="row"><div class="col text-align-left">Boeuf</div><div class="col"><input type="number" name="quantity" min="0" max="5"></div></div>
          <div class="row"><div class="col text-align-left">Poulet</div><div class="col"><input type="number" name="quantity" min="0" max="5"></div></div>
          <div class="row"><div class="col text-align-left">Porc</div><div class="col"><input type="number" name="quantity" min="0" max="5"></div></div>
          <div class="row"><div class="col text-align-left">Végétarien</div><div class="col"><input type="number" name="quantity" min="0" max="5"></div></div>
        </b-modal>
      </div>
    </div>
    <div class="row weekRow " >
      <div class="col align-self-center" >
        <span  >
          Semaine du <span  style="font-weight:bold;color:black"> {{beginDay}} </span> au <span  style="font-weight:bold;color:black">  {{endDay}} </span>  {{month}} {{year}} à <span style="font-weight:bold;color:red"> {{shop}} </span>
        </span>
      </div>
    </div>
    <div class="d-flex justify-content-start SelectGroceryRow " >
      <div>
        <span>Épicerie Sélectionnée : </span>
        <select v-model="shop" @change="fillWeek">
          <option>IGA</option>
          <option>METRO</option>
        </select>
      </div>
    </div>

    <div class="row justify-content-center">
      <div class="col-sm-2 col-xs-12" v-for="day in days" :key="day.name">
        <day :url='day ? day.url : null' :loaded='day != null'>
          <span slot='day-header'>{{day ? day.name : null}}</span>
          <div slot='day-content'>{{day ? day.recipe : null}}</div>
        </day>
      </div>
    </div>

    <div class="d-flex justify-content-end" >
      <div class="price">
        <span> Prix total pour la semaine: <b>{{prix}}$</b></span>
      </div>
    </div>

    <div class="row listContainer" >
      <div class="col bottomPanel GardeManger" style="marginLeft:15px">
        <div>
          <font-awesome-icon icon="utensils"/> Garde manger électronique :
          <div class="row">
          <div class="col-10"><multiselect v-model="inputType" :options="allIngredients" :searchable="true"  :show-labels="false" placeholder="Choisir un ingrédient "></multiselect></div>
          <div class="col-2"><b-button :variant="'success'" @click="addFridgeElmt">Ajouter</b-button></div>
          </div>
           <div class="mt-4">
            <div class="row text-align-left">
                <div class="col">
                  <div class="row" v-for="elmt in fridgeElmt" :key="elmt"><div class="col text-align-left">{{elmt}}</div><div class="col"> <font-awesome-icon icon="trash" class="deleteElmt" @click="deleteFridgeElmt(elmt)"/></div></div>
                </div>
            </div>
           </div>
        </div>
      </div>

      <div class="col bottomPanel listeEpicerie">
        <div class="row">
          <div class="col-11">
        Liste d'épicerie hebdomadaire  :
        </div>
        <div class="col-1 btnPrint" >
          <font-awesome-icon icon="print" />
        </div>
        </div>
        <div class="row text-align-left">
          <div class="col">
            <div class="row" v-for="elmt in toBuyList" :key="elmt"><div class="col text-align-left">{{elmt}}</div></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import day from './day.vue'

const axios = require('axios');

export default {
  name: 'app',
  components: { day },
  data () {
    return {
      prix : 0,
      beginDay: 10,
      endDay: 15,
      year: 2018,
      month: 'Septembre',
      shop: 'IGA',
      days: [null, null, null, null, null],
      linkName:null,
      OptimizedPlan:null,
      inputType:'',
      allIngredients:['test1', 'test2', 'test3'],
      allModifiers:['mod1', 'mod2', 'mod12'],
      fridgeElmt: [],
      toBuyList: ['A REMPLIR', 'A REMPLIR']
    }
  },
  methods: {
    ready () {
      var vm = this
      return axios.post('https://api-recipe.kwidz.fr/optimize').then(function(response) {
        vm.OptimizedPlan = response.data
        vm.fillWeek()
      })
    },
    addFridgeElmt () {
      if(this.inputType) {
        this.fridgeElmt.push(this.inputType)
      }
      
    },
    deleteFridgeElmt (elmt) {
      var index = this.fridgeElmt.indexOf(elmt);
      if (index > -1) {
        this.fridgeElmt.splice(index, 1);
      }
    },
    fillWeek(){
      if (!this.OptimizedPlan) return

      const shop = (this.shop === 'IGA') ? 'weekIGA' : 'weekMetro'

      this.days = [
        {
          name: 'Lundi',
          url: this.OptimizedPlan[shop].monday.url,
          recipe: this.OptimizedPlan[shop].monday.name
        },
        {
          name: 'Mardi',
          url: this.OptimizedPlan[shop].tuesday.url,
          recipe: this.OptimizedPlan[shop].tuesday.name
        },
        {
          name: 'Mercredi',
          url: this.OptimizedPlan[shop].wednesday.url,
          recipe: this.OptimizedPlan[shop].wednesday.name
        },
        {
          name: 'Jeudi',
          url: this.OptimizedPlan[shop].thursday.url,
          recipe: this.OptimizedPlan[shop].thursday.name
        },
        {
          name: 'Vendredi',
          url: this.OptimizedPlan[shop].friday.url,
          recipe: this.OptimizedPlan[shop].friday.name
        }
      ]

      this.prix = (this.shop === 'IGA') ? this.OptimizedPlan.prixIGA
                                        : this.OptimizedPlan.prixMetro
    },

    fillComponentList(){
      var vm=this
      return axios.get('https://api-recipe.kwidz.fr/getTypes').then(function(response){
        vm.allIngredients=response.data.Ingredients
      })
    }
  },

  mounted (){
    this.fillComponentList();
    this.ready();

  }

}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.arrow {
  font-size: 40px;
  color: grey;
  font-weight: bold;
  line-height:150px;
  width: auto;
  height: auto;
  vertical-align: baseline;
  -webkit-align-self: center; /* Safari 7.0+ */
  align-self: center;
}
.arrow:hover {
  cursor: pointer;
}

.leftArrow:after {
  content: '<';
}

.rightArrow:after {
  content: '>';
}

.listContainer {
  height: 60vh
}

.bottomPanel{
  border: solid 1px #282c34;
  border-radius: 5px;
  margin: 15px;
  margin-left: 0px
}


thead {
  background-color: #82C5E3;
}

.deleteElmt:hover, .btnParam:hover {
  cursor: pointer;
}
</style>
