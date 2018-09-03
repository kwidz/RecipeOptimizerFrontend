<template>
  <div id="app" class="container-fluid">
    <div class="row paramRow" >
      <div class="col align-self-left" >
        <div class="btnParam">
          TBK
            <i class="fa fa-cogs" style="font-weight:bold;color:black"></i>
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
    <div class="row dayRow">
    <div class="col leftArrow arrow"></div>
    <div class="col dayContainer divLundi">
      <div class="row dayHeader">Lundi</div>
      <div class="row"></div>
    </div>
    <div class="col dayContainer divMardi">
      <div class="row dayHeader">Mardi</div>
      <div class="row"></div>
    </div>
    <div class="col dayContainer divMercredi">
      <div class="row dayHeader">Mercredi</div>
      <div class="row"></div>    
    </div>
    <div class="col dayContainer divJeudi">
      <div class="row dayHeader">Jeudi</div>
      <div class="row"></div>
    </div>
    <div class="col dayContainer divVendredi">
      <div class="row dayHeader">Vendredi</div>
      <div class="row"></div>
    </div>
    <div class="col rightArrow arrow"></div>
    </div>
    <div class="row listContainer" >
      <div class="col" >
          listContainer
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      beginDay: 10,
      endDay: 17,
      year: 2018,
      month: 'Août',
      shop: 'IGA'
    }
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
  background-color: #eef1f2;
  margin: 15px;
  height: 60vh
}

</style>
