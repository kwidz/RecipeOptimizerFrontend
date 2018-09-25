<template>
<div>
  <div class="day day-loading text-center" v-if="!loaded">
    Chargement en cours...
  </div>

  <div class="day day-loading text-center" v-else-if="deleted">
    Recette supprimée veuillez cliquer à nouveau sur optimiser.
  </div>

    <div class="day text-center" style="z-index:2" v-else>
      <div class="day-header"><slot name='day-header' /><font-awesome-icon style ="float:right; z-index:1 " icon="trash" class="m-1 deleteElmt" @click="deleteRecipe()"/></div>
      <a :href="url">
      <div class="day-content">
        <slot class="recipe-name" name='day-content'/>
        <div class="day-url">Cliquer ici pour voir la recette</div>
      </div>
      </a>
    </div>



  </div>
</template>

<script>
export default {
    props: {
      url: String,
      loaded: Boolean,
      dayName: String,
      grocery: String
    },
    data (){
      return{
        deleted: false
      }
    },
    watch: {
      	loaded: function(newVal, oldVal) { // watch it
          if(oldVal===true && newVal===false)
          this.deleted=false
        }
      },
    methods: {
      deleteRecipe () {
        console.log("test")
        if(!localStorage.recipes)
          localStorage.recipes=JSON.stringify([])
        let plan=JSON.parse(localStorage.OptimizedPlan)
        let forbidenRecipes=JSON.parse(localStorage.recipes)
        if(forbidenRecipes===[])
          console.log("fuck")
        forbidenRecipes.push((plan[this.grocery])[this.dayName])
        localStorage.recipes=JSON.stringify(forbidenRecipes)
        this.deleted=true
      }
    }
}


</script>

<style>
.day {
  background-color: white;
  border: 2px solid #82C5E3;
  text-align: center;
  height: 150px;
  margin-top:10px;
  margin-bottom:10px;
}

.day-content.day-loading {
  background-color: rgba(0.8, 0.8, 0.8, 1);
  color: #efefef;
  font-size: 20px;
  line-height: 150px;
  vertical-align: middle;
}

.day .day-header {
  max-height: 30px;
  color: black;
  background-color: #82C5E3;
  font-size: 20px;
  text-align: center;
}

.day .day-content {
  padding:10px 5px;
}
.day-content .recipe-name{
    color:black;
}
</style>
