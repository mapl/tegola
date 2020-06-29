<template>
<!--  <div id="left-nav" class="sidebar">-->
    <div >
<!--        <div class="toggle2" @click="isActive = !isActive"></div>-->
        <div class="toggle2" @click="tooglesidebar2"
              :class="mytoogleclass">
            <svg class="toggle-arrow2" width="2em" height="2em" viewBox="0 0 16 16" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
                <path fill-rule="evenodd" d="M5.854 4.646a.5.5 0 0 0-.708 0l-3 3a.5.5 0 0 0 0 .708l3 3a.5.5 0 0 0 .708-.708L3.207 8l2.647-2.646a.5.5 0 0 0 0-.708z"/>
                <path fill-rule="evenodd" d="M10 8a.5.5 0 0 0-.5-.5H3a.5.5 0 0 0 0 1h6.5A.5.5 0 0 0 10 8zm2.5 6a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 1 0v11a.5.5 0 0 1-.5.5z"/>
            </svg>
        </div>

  <div id="left-nav" class="sidebar" :class="{
    'sidebar-collapsed' : collapsed,
    'sidebar-expanded': expanded }">
<!--  <div id="left-nav" class="sidebar" :class="{ 'sidebar-collapsed' : isActive }">-->

<!--    <div class="toggle" @click="isActive = !isActive">-->
      <div class="toggle" @click="tooglesidebar">
<!--      <div class="toggle" @click="isActive = !isActive">-->
      <svg class="toggle-arrow" width="2em" height="2em" viewBox="0 0 16 16" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
        <path fill-rule="evenodd" d="M5.854 4.646a.5.5 0 0 0-.708 0l-3 3a.5.5 0 0 0 0 .708l3 3a.5.5 0 0 0 .708-.708L3.207 8l2.647-2.646a.5.5 0 0 0 0-.708z"/>
        <path fill-rule="evenodd" d="M10 8a.5.5 0 0 0-.5-.5H3a.5.5 0 0 0 0 1h6.5A.5.5 0 0 0 10 8zm2.5 6a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 1 0v11a.5.5 0 0 1-.5.5z"/>
      </svg>
      </div>
    <h2><span v-on:click="showAllMaps">Maps</span> <span v-if="activeMap">/ {{activeMap.name}}</span></h2>
    <div class="container">
      <ul id="maps-list" v-if="!activeMap" >
        <MapRow
          v-for="map in capabilities.maps"
          v-bind:key="map.name"
          v-bind:map="map"
        />
      </ul>
      <ul id="map-layers-list" v-if="activeMap && mapIsReady">
        <MapLayerRow
          v-for="layer in activeMap.layers"
          v-bind:key="layer.name"
          v-bind:layer="layer"
        />
      </ul>
    </div>
    <div id="left-nav-footer" v-if="activeMap">
      <div class="btn"
        v-on:click="toggleFeatureInspector"
        v-bind:class="{active: inspectorIsActive}"><span class="dot"></span>Inspect Features
      </div>
    </div>
    </div>
    </div>

</template>

<script>
import MapRow from './MapRow.vue'
import MapLayerRow from './MapLayerRow.vue'
import { store, mutations } from "@/globals/store";
import { map } from "@/globals/map";

// const mapboxgl = require('mapboxgl');
import mapboxgl from "mapbox-gl";

export default {
  name: 'LeftNav',
  components:{
    MapRow,
    MapLayerRow
  },
  props: {
    capabilities: Object
  },
  data(){
    return {
      inspectorIsActive: false,
      inspector: null,
      collapsed: false,
      expanded: true,
      isActive: false,
      mytoogleclass : "",
          }
  },
  computed: {
    activeMap(){
      return store.activeMap
    },
    mapIsReady(){
      return store.mbglIsReady
    },


  },
  methods:{
    tooglesidebar(){
      this.expanded = !this.expanded
      this.collapsed = !this.collapsed
    },

      tooglesidebar2(){

            if (this.mytoogleclass == "sidebar-collapsed2" ){
                this.mytoogleclass = "sidebar-expanded2"
            } else {
                this.mytoogleclass = "sidebar-collapsed2"
            }


          // this.mytoogleclass = "sidebar-collapsed2"
          this.expanded = !this.expanded
          this.collapsed = !this.collapsed

    },
     toggelsidebarclassmethod(){


        return "sidebar-collapsed2"


      },

    // toggleFeatureInspector handles binding and unbinding the mouse events
    // necessary for the feature inspector
    toggleFeatureInspector(){

      if(!this.inspector){
        // new popup instance
        this.inspector = new mapboxgl.Popup();
      }

      if (!this.inspectorIsActive){
        map.on('mousemove', this.inspectFeatures);
        this.inspectorIsActive = true;
      } else {
        map.off('mousemove', this.inspectFeatures);

        this.inspectorIsActive = false;
        if (this.inspector.isOpen()){
          this.inspector.remove();
          this.inspector = null;
        }
      }
    },

    // inspectFeatures handles querying the map instance at the position of the cursor
    // sorting the returned feature keys, building up the HTML fragments and injecting
    // the HTML into a mapbox GL popup instance.
    //
    // TODO (arolek): this should be refactored. It was ported from the original tegola viewer
    // and is quity ugly to look at and maintain. The UX would be better if no popup was used
    // as the feature properties often produce a list longer than the screen.
    inspectFeatures(e){
      var html = '';
      var bbox = {
        width: 10,
        height: 10
      };

      // query within a few pixels of the mouse to give us some tolerance to work with
      var features = map.queryRenderedFeatures([
        [e.point.x - bbox.width / 2, e.point.y - bbox.height / 2],
        [e.point.x + bbox.width / 2, e.point.y + bbox.height / 2]
      ]);

      // everPresent contains the keys that should be "pinned" to the top of the feature inspector. Others
      // will follow and simply be ordered by alpha. See https://github.com/go-spatial/tegola/issues/367
      var everPresent = ['name', 'type', 'featurecla'];
      for (var i=0, l=features.length; i<l; i++){
        html += '<h4>'+features[i].layer.id+'</h4>';
        html += '<ul>';
        html += '<li>feature id <span class="float-r">'+features[i].id+'</span></li>';
        everPresent.forEach(function (key) {
          if (typeof features[i].properties[key] !== "undefined") {
            html += '<li>'+key+'<span class="float-r">'+features[i].properties[key]+'</span></li>'
          }
        });
        Object.keys(features[i].properties).sort().forEach(function (key) {
          if (everPresent.indexOf(key) < 0) {
            html += '<li>'+key+'<span class="float-r">'+features[i].properties[key]+'</span></li>';
          }
        });
        html += '</ul>';
      }

      if (html != '') {
        this.inspector.setLngLat(e.lngLat)
          .setHTML(html)
          .addTo(map);
      } else {
        if (this.inspector.isOpen()){
          this.inspector.remove();
        }
      }
    },

    showAllMaps(){
      // remove the URL hash so the next map load does not use the current map
      // position but rather the init position for that map
      this.removeHash()

      // remove the current active map
      mutations.setActiveMap(null);
    },

    // removes the hash (#) from the URL
    // https://stackoverflow.com/questions/1397329/how-to-remove-the-hash-from-window-location-url-with-javascript-without-page-r/5298684#5298684
    removeHash(){
      history.pushState("", document.title, window.location.pathname+ window.location.search);
    }
  }
}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/*#left-nav {*/
/*  z-index: 100;*/
/*  width: 300px;*/
/*  !*position: fixed;*!*/
/*  position:absolute;*/
/*  background-color: rgba(0,0,0,0.5);*/
/*  display: flex;*/
/*  flex-flow: column;*/
/*  height: 90%;*/
/*  !*top: 57px;*!*/
/*  top: 79px;*/

/*}*/

.sidebar {
  z-index: 100;
  width: 300px;
  /*position: fixed;*/
  position:absolute;
  background-color: rgba(0,0,0,0.5);
  display: flex;
  flex-flow: column;
  height: 90%;
  top: 57px;
  /*top: 79px;*/

}

/*.toggle {*/
/*  position:absolute;*/
/*  !*right:5px;*!*/
/*  right:0px;*/
/*  top:5px;*/
/*  width:20px;*/
/*  height:30px;*/
/*  !*background:white;*!*/
/*  background: rgba(255,255,255,0.9) url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAUCAQAAAAXDMSnAAAAi0lEQVR4AX3JQcqBURQG4O/+9WNG30D3vOfSDTuQsgcZyBakZANSzMVMBme3zsBI5/VMn4ZKLP5ki1E4tYejWpilxVUtzOEUD68odYmXR5BJNp/4zllXD2phllYvamHmirsayUkfJ5ruHzueTldC08kcT5YOY9xYujqQM03XKXuaLmEtNF1e1Nz89gbL+0do6OEwRwAAAABJRU5ErkJggg==) 7px center/7px 10px no-repeat;*/

/*}*/

/*.toggle {*/
/*  position:absolute;*/
/*  width: 0;*/
/*  height: 0;*/
/*  right:-22px;*/
/*  top: -6px;*/
/*  border: 10px solid transparent;*/
/*  border-top: 10px solid;*/
/*  border-right: 10px solid;*/
/*  margin: 15px;*/
/*  transform: rotate(-135deg);*/
/*  color: white;*/
/*}*/

.toggle {
  position:absolute;
  width: 32px;
  height: 32px;
  right:-7px;
  top: 2px;
  cursor: pointer;
  /*background-image: url('data:image/svg+xml;charset=UTF-8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 166 166"><polygon fill="red" points="83 26.8 65.7 61.8 27.1 67.4 55 94.7 48.5 133.2 83 115 117.5 133.2 111 94.7 138.9 67.4 100.3 61.8 83 26.8 83 26.8"/></svg>');*/
  /*background-image: url('data:image/svg+xml;charset=UTF-8,<svg class="bi bi-arrow-bar-left" width="1em" height="1em" viewBox="0 0 16 16" fill="currentColor" xmlns="http://www.w3.org/2000/svg">  <path fill-rule="evenodd" d="M5.854 4.646a.5.5 0 0 0-.708 0l-3 3a.5.5 0 0 0 0 .708l3 3a.5.5 0 0 0 .708-.708L3.207 8l2.647-2.646a.5.5 0 0 0 0-.708z"/>  <path fill-rule="evenodd" d="M10 8a.5.5 0 0 0-.5-.5H3a.5.5 0 0 0 0 1h6.5A.5.5 0 0 0 10 8zm2.5 6a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 1 0v11a.5.5 0 0 1-.5.5z"/>  </svg>');*/
  margin: 0px;
  fill: white;
  /*color: white;*/

}

.toggle2 {
    position:absolute;
    width: 30px;
    height: 30px;
    left: 10px;
    top: 10px;
    cursor: pointer;
    z-index: 101;
    margin: 0px;
    /*background: white;*/
    /*fill: white;*/
    /*color: white;*/

}

.toggle2 .toggle-arrow {

    fill: white;
    transform: rotate(0deg);
}

.toggle-arrow{

  fill:whitesmoke;
  /*filter: brightness(0.2) sepia(1) hue-rotate(180deg) saturate(5);*/
  /*background-color: black;*/
}

/*.toogle-background {*/
/*  background: #c9c9c9;*/
/*  width: 20px;*/
/*  height: 36px;*/
/*  right: 0px;*/
/*  top: 0px;*/
/*  position: absolute;*/

/*}*/
/*.sidebar, .toggle {*/
/*  transition:all .4s ease-in-out;*/
/*  -webkit-transition:all .4s ease-in-out;*/
/*}*/

.sidebar  {
  transition:all 1s ease-in-out;
  /*-webkit-transition:all .2s ease-in-out;*/
}


.sidebar-collapsed {
  transform:translateX(-100%);
  /*-webkit-transform:translateX(-100%);*/
}

.sidebar-collapsed2 .toggle-arrow2
{
    animation-name: rotatearrow;
    /*right: -30px;*/
    animation-duration: 1s;
    /*animation-delay: 0.5s;*/
    animation-fill-mode: forwards;
    animation-direction: normal;
    /*animation-play-state: running;*/
    /*transform: rotate(180deg);*/
    /*-webkit-transform:translateX(100%);*/
}

.sidebar-expanded2 .toggle-arrow2
{
    /*transform:translateX(-20%);*/

    animation-name: rotatearrow2;
    /*right: -30px;*/
    animation-duration: 1s;
    /*!*animation-delay: 0.5s;*!*/
    animation-fill-mode: forwards;
    animation-direction: normal;
    /*animation-play-state: running;*/
    /*transform: rotate(180deg);*/
    /*-webkit-transform:translateX(100%);*/
}



@keyframes rotatearrow {
    0% {
        transform: rotate(0deg) ;
        /*right: -30px;*/

    }
    100%   {
        /*transform: translateX(-40px) rotate(180deg);*/
        /*transform: translateX(60px) ;*/
        /*right: -30px;*/
        transform: rotate(180deg) ;
    }
}

@keyframes rotatearrow2 {
    0% {
        transform: rotate(180deg) ;
        /*right: -30px;*/

    }
    100%   {
        /*transform: translateX(-40px) rotate(180deg);*/
        /*transform: translateX(60px) ;*/
        /*right: -30px;*/
        transform: rotate(0deg) ;
    }
}

.sidebar-collapsed .toggle-arrow
{
   animation-name: slidein;
  /*right: -30px;*/
  animation-duration: 0.5s;
  animation-delay: 0.5s;
  animation-fill-mode: forwards;
  animation-direction: normal;
  /*animation-play-state: running;*/
  /*transform: rotate(180deg);*/
  /*-webkit-transform:translateX(100%);*/
}

/*.sidebar .toggle {*/
/*  !*right:-30px;*!*/
/*  animation-name: slideback;*/
/*  !*right: -30px;*!*/
/*  animation-duration: 1s;*/
/*  animation-delay: 0.5s;*/
/*  animation-fill-mode: forwards;*/
/*  !*animation-fill-mode: none;*!*/
/*  !*animation-direction: reverse;*!*/
/*  !*animation-play-state: unset;*!*/
/*  !*transform:translateX(100%);*!*/
/*  !*transform: rotate(180deg);*!*/
/*  transform:  rotate(180deg);*/
/*  !*-webkit-transform:translateX(100%);*!*/
/*}*/

.sidebar-expanded .toggle-arrow {
  /*right:-30px;*/
  animation-name: slideback;
  /*right: -30px;*/
  animation-duration: 0.5s;
  animation-delay: 0.5s;
  animation-fill-mode: forwards;
  /*animation-fill-mode: none;*/
  /*animation-direction: reverse;*/
  /*animation-play-state: unset;*/
  /*transform:translateX(100%);*/
  /*transform: rotate(180deg);*/
  transform:  rotate(180deg);
  /*-webkit-transform:translateX(100%);*/
}


@keyframes slidein {
  0% {
    /*transform: rotate(0deg) ;*/
    /*right: -30px;*/

  }
  100%   {
    /*transform: translateX(-40px) rotate(180deg);*/
    /*transform: translateX(60px) ;*/
    /*right: -30px;*/
    transform: translateX(20px) rotate(180deg) ;
  }
}

@keyframes slideback {
  0% {
    transform:  rotate(180deg);
    /*right: 30px;*/

  }
  100%   {
    transform:  rotate(0deg);
    /*right: -30px;*/
  }
}


.container {
  width: 100%;
  flex: 1 1 auto;
  overflow-y: scroll;
}

#left-nav-footer {
  flex: 0 1 40px;
}


h2 {
  padding: 10px;
  margin: 0;
  font-size: 14px;
  border-bottom: 1px solid #ccc;
}
h2 span {
  cursor: pointer;
}

#maps-list {
  margin: 0;
  padding: 0;
  font-size: 14px;
  height: 100%;
}

#map-layers-list {
  display: flex;
  flex-flow: column;
  height: 100%;
  margin: 0;
  padding: 0;
  list-style: none;
  font-size: 14px;
}

.btn {
  display: block;
  padding: 6px 12px;
  margin: 5px;
  font-size: 14px;
  font-weight: 400;
  line-height: 1.42857143;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  cursor: pointer;
  user-select: none;
  border: 1px solid #444;
  border-radius: 4px;
}
.btn:hover {
  border-color: #666;
  color: #eee;
}
.btn .dot{
  border-radius: 2px;
  width: 8px;
  height: 8px;
  display: inline-block;
  background-color: #333;
  margin-right: 6px;
}
.btn.active .dot{
  background-color: #259b24;
}
</style>
