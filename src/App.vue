<template>
  <div id="app" class="container-fluid">
    <div class="row paramRow" >
<div class="col-11"></div>
      <div class="col align-self-right" >
        <div class="btnParam" style="font-weight:bold;color:grey;font-size:30px;">
          <font-awesome-icon icon="cogs" />
        </div>
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
    <div class="row dayRow">
      <div class="col leftArrow arrow"></div>
      <div class="col dayContainer divLundi">
        <div class="row dayHeader"><div class="col align-self-center" >Lundi</div></div>
        <div class="row RecipeName">
          {{RecipeMondayName}}
        </div>
        <div class="row recipeURL">
          <a :href="RecipeMondayURL">Cliquer ici pour voir la recette</a>
        </div>
      </div>
      <div class="col dayContainer divMardi">
        <div class="row dayHeader"><div class="col align-self-center" >Mardi</div></div>
        <div class="row RecipeName">
          {{RecipeTuesdayName}}
        </div>
        <div class="row recipeURL">
          <a :href="RecipeTuesdayURL">Cliquer ici pour voir la recette</a>
        </div>
      </div>
      <div class="col dayContainer divMercredi">
        <div class="row dayHeader"><div class="col align-self-center" >Mercredi</div></div>
        <div class="row RecipeName">
          {{RecipeWednesdayName}}
        </div>
        <div class="row recipeURL">
          <a :href="RecipeWednesdayURL">Cliquer ici pour voir la recette</a>
        </div>
      </div>
      <div class="col dayContainer divJeudi">
        <div class="row dayHeader"><div class="col align-self-center" >Jeudi</div></div>
        <div class="row RecipeName">
          {{RecipeThursdayName}}
        </div>
        <div class="row recipeURL">
          <a :href="RecipeThursdayURL">Cliquer ici pour voir la recette</a>
        </div>
      </div>
      <div class="col dayContainer divVendredi">
        <div class="row dayHeader"><div class="col align-self-center" >Vendredi</div></div>
        <div class="row RecipeName">
          {{RecipeFridayName}}
        </div>
        <div class="row recipeURL">
          <a :href="RecipeFridayURL">Cliquer ici pour voir la recette</a>
        </div>
      </div>
      <div class="col rightArrow arrow"></div>
    </div>

    <div class="d-flex justify-content-end" >

        <div class="price">
          <span> Prix total pour la semaine: {{prix}}$</span>
        </div>

    </div>

    <div class="row listContainer" >
      <div class="col bottomPanel GardeManger" style="marginLeft:15px">
        <div>
          <font-awesome-icon icon="utensils"/> Garde manger électronique :
          <multiselect v-model="inputType" :options="allIngredients" :searchable="true"  :show-labels="false" placeholder="Choisir un ingrédient "></multiselect>
           <div class="mt-4">
            <table class="table table-bordered pagin-table">
                <thead>
                    <tr>
                        <th>Name</th>
                        <th width="220px">Action</th>
                    </tr>
                </thead>
                <tbody sortable>
                    <tr>
                      <td>2 Tomates Savani</td>
                      <td><a href="" class="btn btn-danger">Delete</a></td>
                    </tr>
                    <tr>
                      <td>1 kg de boeuf haché</td>
                      <td><a href="" class="btn btn-danger">Delete</a></td>
                    </tr>
                </tbody>
            </table>
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
            <ul>
              <li>Coffee</li>
              <li>Tea</li>
              <li>Milk</li>
            </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require('axios');
export default {
  name: 'app',
  data () {
    return {
      prix : 0,
      beginDay: 10,
      endDay: 15,
      year: 2018,
      month: 'Septembre',
      shop: 'IGA',
      RecipeMondayName:null,
      RecipeMondayURL:null,
      RecipeTuesdayName:null,
      RecipeTuesdayURL:null,
      RecipeWednesdayName:null,
      RecipeWednesdayURL:null,
      RecipeThursdayName:null,
      RecipeThursdayURL:null,
      RecipeFridayName:null,
      RecipeFridayURL:null,
      OptimizedPlan:null,
      inputType:'',
      allIngredients:['test1', 'test2', 'test3'],
      allModifiers:['mod1', 'mod2', 'mod12']
    }
  },
  methods: {
    ready () {
      var vm = this
      return axios.get('https://api-recipe.kwidz.fr/optimize').then(function(response) {
        vm.OptimizedPlan=response.data
        vm.fillWeek()
      })
    },
    fillWeek(){
      if(this.OptimizedPlan){
        if(this.shop==='IGA'){
          this.RecipeMondayName = this.OptimizedPlan.weekIGA.monday.name,
          this.RecipeMondayURL = this.OptimizedPlan.weekIGA.monday.url,
          this.RecipeTuesdayName = this.OptimizedPlan.weekIGA.tuesday.name,
          this.RecipeTuesdayURL = this.OptimizedPlan.weekIGA.tuesday.url,
          this.RecipeWednesdayName = this.OptimizedPlan.weekIGA.wednesday.name,
          this.RecipeWednesdayURL = this.OptimizedPlan.weekIGA.wednesday.url,
          this.RecipeThursdayName = this.OptimizedPlan.weekIGA.thursday.name,
          this.RecipeThursdayURL = this.OptimizedPlan.weekIGA.thursday.url,
          this.RecipeFridayName = this.OptimizedPlan.weekIGA.friday.name,
          this.RecipeFridayURL = this.OptimizedPlan.weekIGA.friday.url,
          this.prix=this.OptimizedPlan.prixIGA
        }
        if(this.shop==='METRO'){
          this.RecipeMondayName = this.OptimizedPlan.weekMetro.monday.name,
          this.RecipeMondayURL = this.OptimizedPlan.weekMetro.monday.url,
          this.RecipeTuesdayName = this.OptimizedPlan.weekMetro.tuesday.name,
          this.RecipeTuesdayURL = this.OptimizedPlan.weekMetro.tuesday.url,
          this.RecipeWednesdayName = this.OptimizedPlan.weekMetro.wednesday.name,
          this.RecipeWednesdayURL = this.OptimizedPlan.weekMetro.wednesday.url,
          this.RecipeThursdayName = this.OptimizedPlan.weekMetro.thursday.name,
          this.RecipeThursdayURL = this.OptimizedPlan.weekMetro.thursday.url,
          this.RecipeFridayName = this.OptimizedPlan.weekMetro.friday.name,
          this.RecipeFridayURL = this.OptimizedPlan.weekMetro.friday.url,
          this.prix=this.OptimizedPlan.prixMetro
        }
      }
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

.dayContainer {
  background-color: white;
  border: 2px solid #82C5E3;
  text-align: center;
  margin: 15px;
  width: 100px;
  height: 150px;
}
.dayRow {
  margin: 15px;
  vertical-align: baseline;
  -webkit-align-self: center; /* Safari 7.0+ */
  align-self: center;
}
.arrow {
  font-size: 40px;
  color: grey;
  font-weight: bold;
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
.dayHeader {
  max-height: 30px;
  color: black;
  background-color: #82C5E3;
  font-size: 20px;
  text-align: center;
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
  
</style>
