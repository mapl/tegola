<template>
  <div id="header">
<!--    <div class="toggle" @click="isActive = !isActive" >-->
<!--      <svg class="toggle-arrow" width="2em" height="2em" viewBox="0 0 16 16" fill="currentColor" xmlns="http://www.w3.org/2000/svg">-->
<!--        <path fill-rule="evenodd" d="M5.854 4.646a.5.5 0 0 0-.708 0l-3 3a.5.5 0 0 0 0 .708l3 3a.5.5 0 0 0 .708-.708L3.207 8l2.647-2.646a.5.5 0 0 0 0-.708z"/>-->
<!--        <path fill-rule="evenodd" d="M10 8a.5.5 0 0 0-.5-.5H3a.5.5 0 0 0 0 1h6.5A.5.5 0 0 0 10 8zm2.5 6a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 1 0v11a.5.5 0 0 1-.5.5z"/>-->
<!--      </svg>-->
<!--    </div>-->
<!--    <h1 class="toggle" >12</h1>-->
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
.toggle{
  height: 40px;
  width: 40px;
  cursor: pointer;
  top: 0;
  left: 10px;
  /*text-align: center;*/
  /*text-justify: auto;*/
  /*position: fixed;*/
  /*position: absolute;*/
  position: relative;
  /*padding: 8px;*/
  margin: 5px;
  /*background-color: white;*/
  background-color: gray;
  box-sizing: border-box;
  /*padding: 12px;*/

}

.sidebar  {
  transition:all 1s ease-in-out;
  /*-webkit-transition:all .2s ease-in-out;*/
}


.sidebar-collapsed {
  transform:translateX(-100%);
  /*-webkit-transform:translateX(-100%);*/
}


h1 {
  /*padding: 5px;*/
  /*top: 0px;*/
  left: 50px;
  /*border: 10px;*/
  line-height: 50px;
  font-weight: bold;
  font-size: 16px;
  position: relative;
  margin: 0;
  box-sizing: border-box;
  /*margin: 0 0 0 10px;*/
  /*position: absolute;*/
  /*position: fixed;*/
}
h2 {
  padding: 10px;
  margin: 0;
  font-size: 14px;
  border-bottom: 1px solid #ccc;
}
</style>
