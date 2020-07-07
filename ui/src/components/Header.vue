<template>
  <div id="header">
    <h1>Tegola ({{capabilities.version}})</h1>
  </div>
</template>

<script>
export default {
  name: 'Header',
  props:{
    capabilities: Object
  },
  data(){
    return {
      isActive: false,
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#header {
  width: 100%;
  z-index: 100;
  /*position: fixed;*/
  position: absolute;
  /*position: relative;*/
  border-bottom: 1px solid #eaeaea;
  background-color: rgba(0,0,0,0.5);
  /*display: flex;*/


}
/*.toggle{*/
/*  height: 40px;*/
/*  width: 40px;*/
/*  cursor: pointer;*/
/*  top: 0;*/
/*  left: 10px;*/
/*  !*text-align: center;*!*/
/*  !*text-justify: auto;*!*/
/*  !*position: fixed;*!*/
/*  !*position: absolute;*!*/
/*  position: relative;*/
/*  !*padding: 8px;*!*/
/*  margin: 5px;*/
/*  !*background-color: white;*!*/
/*  background-color: gray;*/
/*  box-sizing: border-box;*/
/*  !*padding: 12px;*!*/

/*}*/

/*.sidebar  {*/
/*  transition:all 1s ease-in-out;*/
/*  !*-webkit-transition:all .2s ease-in-out;*!*/
/*}*/


.sidebar-collapsed {
  transform:translateX(-100%);
  /*-webkit-transform:translateX(-100%);*/
}


/*h1 {*/
/*  padding: 8px;*/
/*  !*top: 0px;*!*/
/*  !*left: 150px;*!*/
/*  !*border: 10px;*!*/
/*  line-height: 40px;*/
/*  font-weight: bold;*/
/*  font-size: 1em;*/
/*  position: relative;*/
/*  margin: 0;*/
/*  top: 0px*/
/*  !*top: 0px;*!*/
/*  !*box-sizing: border-box;*!*/
/*  !*margin: 0 0 0 10px;*!*/
/*  !*position: absolute;*!*/
/*  !*position: fixed;*!*/
/*}*/

h1 {
    padding: 8px;
    top: 0;
    line-height: 40px;
    font-weight: bold;
    font-size: 16px;
    margin: 0;
}

h2 {
  padding: 10px;
  margin: 0;
  font-size: 14px;
  border-bottom: 1px solid #ccc;
}
</style>
