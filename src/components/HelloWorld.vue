<template>
  <div id="HelloWorld">
    <h2> Lealeft </h2>
    <div class="column">
      <!-- <BrewMap ></BrewMap> -->

      <div class="map">
          <l-map :zoom="zoom" :center="center" >
            <l-tile-layer 
              :url="url">
            </l-tile-layer>
            <l-marker :lat-lng="markerLatLng" ></l-marker>
            
            <l-polygon
              v-on:click="onMapClick"
              :key="polygon.id"
              :lat-lngs="polygon.latlngs"
              :color="polygon.color"
              @l-mouseover="change_color(polygon)">
              <l-popup :content="popup.content"/>
            </l-polygon>

          </l-map>
      </div>

    

    </div>
  </div>
</template>

<script>
import axios from 'axios'
// import BrewMap from './BrewMap'
import {LMap, LTileLayer, LMarker, LPolygon, LPopup} from 'vue2-leaflet';

export default {
  name: "HelloWorld",
  components: {
    // BrewMap,
    LMap,
    LTileLayer,
    LMarker,
    LPolygon,
    LPopup
  },
  data () {
    return {
      puntos: null,
      url: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
      zoom: 13,
      center: [0.43583,48.01156],
      markerLatLng: [0.42183,48.01156],
      polygon: {
        id: 'my_id',
        latlngs: [],
        color: 'blue',
      },
      popup: {
        id: 1,
        latlng: L.latLng(47.417220, -1.25),
        content: 'Another',
      }
    }
  },
  mounted () {
    axios.get("https://rawgit.com/gregoiredavid/france-geojson/master/regions/pays-de-la-loire/communes-pays-de-la-loire.geojson")
      .then((response) => {
        this.polygon.latlngs = response.data.features[0].geometry.coordinates[0]
      })
  },
  methods: {
  	change_color(polygon) {
    	polygon.color = 'green';
    },
    onMapClick(e){
      this.popup.content = "Coordenadas (" + e.latlng.lat +" " + e.latlng.lng +")",
      this.popup.content = 
        "<div style='display: flex; border: 1px solid gray;border-radius: 7px;padding: 10px 5px;'><ul style='margin:0; padding: 0; width: 70%;'><li style='display:flex; list-style: none; margin-bottom: 5px;'> <p style='margin:0; width: 60px;'>Muestras</p> <input style='width: 80px;' name='muestras' placeholder='1'></li> <li style='display:flex; list-style: none;'><p style='margin:0; width: 60px;'>Radio(m)</p> <input style='width: 80px;'  name='radio' placeholder='10'></li></ul><div style='display: flex;flex-direction: column;justify-content: flex-start; align-items: center;'><a style='margin-bottom: 10px;' href='' v-on:click='borrar'>Borrar</a> <a href='' v-on:click='guardar'>Guardar</a></div></div> <p>"+e.latlng.lat+","+e.latlng.lng+"</p>",
      this.markerLatLng = [e.latlng.lat,e.latlng.lng]
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.column {
  margin: 1%;
  text-align: left;
}
.map {
  height: 80vh;
}
.popup {
  border: 1px solid red;
}
.leaflet-fade-anim .leaflet-map-pane .leaflet-popup {
  bottom: 35px !important;
}
</style>
