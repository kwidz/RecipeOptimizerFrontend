<template>
  <div id="app" class="container-fluid">
    <div class="row weekRow" >
      <div class="col-sm-12 col-md-8 offset-md-2 text-center order-12 order-sm-1">
        Semaine du <span  style="font-weight:bold;color:black"> {{beginDay}} </span>
        au <span  style="font-weight:bold;color:black">  {{endDay}} </span>
        {{month}} {{year}} à <span style="font-weight:bold;color:red"> {{shop}} </span>
      </div>
      <div class="col-sm-12 col-md-2  order-1 order-sm-12 text-center">
        <div class="btnParam" style="font-weight:bold;color:grey;font-size:30px;">
          <b-button :variant="'normal'" v-b-modal.modalParam><font-awesome-icon icon="cogs"  /> Paramètres</b-button>
        </div>
        <b-modal id="modalParam" title="Paramètres" ref="modal" @ok="setParam" >
          <p class="my-4">Paramètrage de votre semaine</p>
          <label>Choisissez le nombre de type de repas que vous souhaitez dans votre semaine</label>
          <div class="row"><div class="col text-align-left">Poisson</div><div class="col"><input type="number" v-model="quantityPoisson" min="0" max="5" /></div></div>
          <div class="row"><div class="col text-tomalign-left">Boeuf</div><div class="col"><input type="number" v-model="quantityBoeuf" min="0" max="5" /></div></div>
          <div class="row"><div class="col text-align-left">Poulet</div><div class="col"><input type="number" v-model="quantityPoulet" min="0" max="5" /></div></div>
          <div class="row"><div class="col text-align-left">Porc</div><div class="col"><input type="number" v-model="quantityPorc" min="0" max="5"  /></div></div>
          <div class="row"><div class="col text-align-left">Végétarien</div><div class="col"><input type="number" v-model="quantityVegan" min="0" max="5"/></div></div>
        </b-modal>
      </div>
    </div>
    <div class="d-flex justify-content-start SelectGroceryRow m-4" >
      <div class="col-12">
        <span>Épicerie Sélectionnée : </span>
        <select v-model="shop" @change="fillWeek" >
          <option>IGA</option>
          <option>METRO</option>
        </select>
      </div>
    </div>

    <div class="row justify-content-center ">
      <div class="col-10 m-4">
          <b-button :variant="'success'" @click="optimize">Optimiser la liste des repas</b-button>
      </div>
    </div>

    <div class="row justify-content-center">

      <div class="col-md-2 col-sm-12" v-for="day in days" >
        <day :url='day ? day.url : null' :loaded="loaded" :grocery=' day ? day.grocery : null' :dayName='day ? day.dayName : null'>
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

    <div class="row row-eq-height" >
      <div class="col-md-6 col-sm-12">
        <div class="bottomPanel GardeManger">
          <font-awesome-icon icon="utensils"/> Garde manger électronique :
          <div class="row">
            <div class="col-md-10 col-sm-12 order-sm-1"><multiselect v-model="inputType" :options="allIngredients" :searchable="true"  :show-labels="false" placeholder="Choisir un ingrédient "></multiselect></div>
            <div class="col-md-2 col-sm-12 order-sm-2"><b-button :variant="'success'" @click="addFridgeElmt">Ajouter</b-button></div>
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

      <div class="col-md-6 col-sm-12 ">
        <div class="bottomPanel listeEpicerie">
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
      loaded :true,
      prix : 0,
      beginDay: 24,
      endDay: 29,
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
      toBuyList: ['A REMPLIR', 'A REMPLIR2'],
      quantityPoisson:1,
      quantityBoeuf:1,
      quantityPoulet:1,
      quantityPorc:1,
      quantityVegan:1
    }
  },
  methods: {
    ready () {
      this.loaded=false
      if(localStorage.types){
      let types = JSON.parse(localStorage.types)
      types.forEach(type=>{
        this.fridgeElmt.push(type)
        })
      }
      if(!localStorage.balance)
        localStorage.balance=JSON.stringify([1,1,1,1,1])
      if(!localStorage.types)
        localStorage.types=JSON.stringify([])
      if(!localStorage.recipes)
          localStorage.recipes=JSON.stringify([])

      this.quantityPoisson=localStorage.balance ? JSON.parse(localStorage.balance)[4] : 1
      this.quantityBoeuf=localStorage.balance ? JSON.parse(localStorage.balance)[1] : 1
      this.quantityPoulet=localStorage.balance ? JSON.parse(localStorage.balance)[3] : 1
      this.quantityPorc=localStorage.balance ? JSON.parse(localStorage.balance)[2] : 1
      this.quantityVegan=localStorage.balance ? JSON.parse(localStorage.balance)[0] : 1

      if(!localStorage.OptimizedPlan){
        console.log('searched');
        return axios.post('https://api-recipe.kwidz.fr/optimize').then(response => {
          this.OptimizedPlan = response.data
          localStorage.OptimizedPlan = JSON.stringify(response.data)
          this.fillWeek()
          })
      }
      else {
        console.log('stored');
        this.OptimizedPlan=JSON.parse(localStorage.OptimizedPlan)
        this.fillWeek()
      }

    },
    optimize(){
      console.log("optimization !")

      /*
        This request is composed of an array of forbiden recipes named recipes
         containing the whole recipe json object
        an array of types named types containing all types that the user added
        and an array of int named balance wich is containing the number of each recipe type per week
        order is Vegetarian, beef, pork, chiken and fish based recipes.
      */
      //first lets get the recipes
      let recipes = JSON.parse(localStorage.recipes)
      //get the get the Types
      let types = JSON.parse(localStorage.types)
      //finaly get the balance
      let balance = JSON.parse(localStorage.balance)
      let jsonObject={'recipes':recipes, 'types':types, 'balance':balance}
      this.loaded=false
      axios.post('https://api-recipe.kwidz.fr/optimize',(jsonObject)).then(response => {

        this.OptimizedPlan = response.data
        localStorage.OptimizedPlan = JSON.stringify(response.data)
        this.fillWeek()
        })


    },
    setParam (evt) {
      // Prevent modal from closing
      evt.preventDefault()
      let sum=parseInt(this.quantityPoisson) + parseInt(this.quantityBoeuf) + parseInt(this.quantityPoulet) + parseInt(this.quantityPorc) + parseInt(this.quantityVegan)
      console.log(sum)
      if ( sum>5) {
        alert('la somme des types doit être inférieure ou égale a 5\n'+this.quantityPoisson+"\n"+this.quantityBoeuf+"\n"+this.quantityPoulet+"\n"+this.quantityPorc+"\n"+this.quantityVegan+"\n")
      } else {
        localStorage.balance=JSON.stringify([this.quantityVegan, this.quantityBoeuf,this.quantityPorc,this.quantityPoulet,this.quantityPoisson,])
        this.$refs.modal.hide()
      }
    },
    addFridgeElmt () {
      if(this.inputType) {
        this.fridgeElmt.push(this.inputType)
        if(!localStorage.types)
        localStorage.types=JSON.stringify([])
        let types = JSON.parse(localStorage.types)
        types.push(this.inputType.toUpperCase())
        localStorage.types = JSON.stringify(types)
      }

    },
    deleteFridgeElmt (elmt) {

      var index = this.fridgeElmt.indexOf(elmt)
      if (index > -1) {
        this.fridgeElmt.splice(index, 1)
      }
      if(!localStorage.types)
        localStorage.types=JSON.stringify([])
      let types = JSON.parse(localStorage.types)
      index = types.indexOf(elmt.toUpperCase())
      if (index > -1) {
        types.splice(index, 1)
      }
      localStorage.types=JSON.stringify(types)
    },
    fillWeek(){
      this.loaded=true
      if (!this.OptimizedPlan) return

      let shop = (this.shop === 'IGA') ? 'weekIGA' : 'weekMetro'
      console.log(shop)
      this.days = [
        {

          name: 'Lundi',
          url: this.OptimizedPlan[shop].monday.url,
          recipe: this.OptimizedPlan[shop].monday.name,
          grocery: shop,
          dayName:'monday'
        },
        {
          name: 'Mardi',
          url: this.OptimizedPlan[shop].tuesday.url,
          recipe: this.OptimizedPlan[shop].tuesday.name,
          grocery: shop,
          dayName:'tuesday'
        },
        {
          name: 'Mercredi',
          url: this.OptimizedPlan[shop].wednesday.url,
          recipe: this.OptimizedPlan[shop].wednesday.name,
          grocery: shop,
          dayName:'wednesday'
        },
        {
          name: 'Jeudi',
          url: this.OptimizedPlan[shop].thursday.url,
          recipe: this.OptimizedPlan[shop].thursday.name,
          grocery: shop,
          dayName:'thursday'
        },
        {
          name: 'Vendredi',
          url: this.OptimizedPlan[shop].friday.url,
          recipe: this.OptimizedPlan[shop].friday.name,
          grocery: shop,
          dayName:'friday'
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
    this.fillComponentList()
    this.ready()

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

.bottomPanel{
  border: solid 1px #282c34;
  border-radius: 5px;
  padding:10px;
  height:100%;
}


thead {
  background-color: #82C5E3;
}

.deleteElmt:hover, .btnParam:hover {
  cursor: pointer;
}
</style>
