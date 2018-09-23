<template>
  <a :href="url" v-if="loaded">
    <div class="day text-center">
      <div class="day-header"><slot name='day-header' /></div>
      <div class="day-content">
        <slot name='day-content'/>
        <div class="day-url">Cliquer ici pour voir la recette</div>
      </div>
    </div>
  </a>
  <div class="day day-loading text-center" v-else>
    Chargement en cours...
  </div>
</template>

<script>
export default {
    props: {
      url: String,
      loaded: Boolean
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

.day.day-loading {
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
</style>
