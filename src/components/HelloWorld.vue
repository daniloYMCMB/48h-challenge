<template>
  <div id="HelloWorld">
    <h2 > Front End -- 48h challenge </h2>
    <div class="column">
      <!-- <BrewMap ></BrewMap> -->
      <div class="menu">
        <ul class="ul">
          <li class="li"> 
            <p class="p">Muestras:</p> 
            <input v-model='muestras' type="number" class="input" placeholder='1'>
          </li>
          <li class="li">
            <p class="p">Radio(m):</p> 
            <input v-model='radio' type="number" class="input" placeholder='10'>
          </li>
        </ul>
        <div class="btns">
          <a style='margin-bottom: 7px;' v-on:click='borrar'>Borrar</a>
          <a v-on:click='guardar'>Guardar</a>
        </div>
      </div>

      <div class="map">
          <l-map :zoom="zoom" :center="center" >
            <l-tile-layer :url="url"></l-tile-layer>
            <l-marker :lat-lng="markerLatLng" ></l-marker>
            <l-circle :lat-lng="circle.center" 
                      :radius="radio" 
                      :color="circle.color"></l-circle>
            <l-polygon v-on:click="onMapClick"
                       :key="polygon.id"
                       :lat-lngs="polygon.latlngs"
                       :color="polygon.color">
              <l-popup :content="popup.content"></l-popup>
            </l-polygon>
          </l-map>
      </div>

    </div>
  </div>
</template>

<script>
import axios from 'axios'
import {LMap, LTileLayer, LMarker, LPolygon, LPopup, LCircle} from 'vue2-leaflet';

export default {
  name: "HelloWorld",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPolygon,
    LPopup,
    LCircle,
  },
  data () {
    return {
      puntos: null,
      url: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
      zoom: 17,
      center: [0.42183,48.01156],
      markerLatLng: [0.42183,48.01156],
      polygon: {
        id: 'my_id',
        latlngs: [],
        color: 'blue',
      },
      popup: {
        id: 1,
        latlng: L.latLng(47.417220, -1.25),
        content: '',
      },
      circle: {
        center: [0.42183,48.01156],
        radius: 40,
        color: 'red',
        inputRadio: 5
      },
      radio: 10,
      muestras: '',
      markers: [
        {
          "position": {
            "latitude": -12.3435345,
            "longitude": 73.234234
          },
          "radio": 12,
          "num_samples":3
        }
      ],
      latitude: '',
      longitude: '',
      click: false
    }
  },
  mounted () {
    axios.get("https://rawgit.com/gregoiredavid/france-geojson/master/regions/pays-de-la-loire/communes-pays-de-la-loire.geojson")
      .then((response) => {
        this.polygon.latlngs = response.data.features[0].geometry.coordinates[0]
      })
  },
  methods: {
    onMapClick(e){
      this.click = true
      this.latitude = e.latlng.lat
      this.longitude = e.latlng.lng
      this.popup.content = 
        "<div style='display: flex; border: 1px solid gray;border-radius: 7px;padding: 10px 5px;'><ul style='margin:0; padding: 0; width: 70%;'><li style='display:flex; list-style: none; margin-bottom: 5px;'> <p style='margin:0; width: 60px;'>Muestras</p> <input style='width: 80px;' name='muestras' placeholder='1'></li> <li style='display:flex; list-style: none;'><p style='margin:0; width: 60px;'>Radio(m)</p> <input v-model='message' style='width: 80px;' name='radio' placeholder='10'></li></ul><div style='display: flex;flex-direction: column;justify-content: flex-start; align-items: center;'> <a style='margin-bottom: 10px;' v-on:click='borrar'>Borrar</a> <a v-on:click='guardar'>Guardar</a></div></div> <p>"+e.latlng.lat+","+e.latlng.lng+"</p>",

      this.markerLatLng = [e.latlng.lat,e.latlng.lng]
      this.radio = 10
      this.circle.center = [e.latlng.lat,e.latlng.lng]
      
      var boxPopup = document.getElementsByClassName("leaflet-popup")
      for (var i = 0; i < boxPopup.length; i++) {
        boxPopup[i].style.display = "block";
      }
      var shadowPanel = document.getElementsByClassName("leaflet-shadow-pane")
      for (var i = 0; i < shadowPanel.length; i++) {
        shadowPanel[i].style.display = "block";
      }
      var markerPanel = document.getElementsByClassName("leaflet-marker-pane")
      for (var i = 0; i < markerPanel.length; i++) {
        markerPanel[i].style.display = "block";
      }
      var overlayPanel = document.querySelectorAll(".leaflet-overlay-pane path:first-child")
      for (var i = 0; i < overlayPanel.length; i++) {
        overlayPanel[i].style.display = "block";
      }
    },
    borrar(e){
      this.radio = 10
      var boxPopup = document.getElementsByClassName("leaflet-popup")
      for (var i = 0; i < boxPopup.length; i++) {
        boxPopup[i].style.display = "none";
      }
      var shadowPanel = document.getElementsByClassName("leaflet-shadow-pane")
      for (var i = 0; i < shadowPanel.length; i++) {
        shadowPanel[i].style.display = "none";
      }
      var markerPanel = document.getElementsByClassName("leaflet-marker-pane")
      for (var i = 0; i < markerPanel.length; i++) {
        markerPanel[i].style.display = "none";
      }
      var overlayPanel = document.querySelectorAll(".leaflet-overlay-pane path:first-child")
      for (var i = 0; i < overlayPanel.length; i++) {
        overlayPanel[i].style.display = "none";
      }
    },
    guardar(e){
      if(this.click) {
        this.markers.push(""+
          "{"+
              "position: {"+
              "latitude: "+ this.latitude+","+
              "longitude: "+ this.longitude+""+
              "},"+
              "radio: "+ this.radio+","+
              "num_samples: "+ this.muestras+""+
          "}"+
        "")
        console.log(JSON.stringify(this.markers))
      }
      this.click = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.column {
  margin: 1%;
  text-align: left;
  position: relative;
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
.menu {
  display: flex; 
  border: 1px solid gray;
  border-radius: 7px;
  padding: 10px 5px;
  width: 280px;
  margin-bottom: 10px;
  position: absolute;
  top: 10px;
  left: 55px;
  z-index: 99999;
  background: white;
}
.ul {
  margin:0; 
  padding: 0; 
  width: 70%;
}
.li {
  display:flex; 
  list-style: none; 
  margin-bottom: 5px;
}
.li:last-child {
  margin-bottom: 0;
}
.p {
  margin:0;
  width: 70px;
}  
.input {
  width: 80px;
  margin-bottom: 10px;
} 
.btns{
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-end;
}
.btns a{  
  padding: 3px 15px;
  background: green;
  color: white;
  border-radius: 7px;
  font-size: 14px;
  cursor: pointer;
}
</style>
