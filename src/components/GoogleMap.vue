<template>

<div class="row">

    <div class="col-sm-9 col-md-9" >
        <!-- Map Loader -->
        <gmap-map @click="mapclicked"
            :center="center"
            :zoom="8"
            style="width: 100%; height: 100vh"
        >
        <!-- Markers Container -->
        <gmap-marker
            :key="index"
            v-for="(m, index) in markers"
            :position="m.position"
            :clickable="true"
            :draggable="false"
            :opacity="m.opacity"
            @click="center=m.position"
            >
            <!-- Info Window -->
            <gmap-info-window :opened="m.ifw">{{m.PlaceName}}
            <input type="text" maxlength="20" placeholder="Enter Address" ref="input" v-show="m.infoArea">
            </input></br>
            <button class="infoWindowButton btn btn-primary btn-sm" type="submit" v-show="m.infoArea" @click="handleInfo(index)">Save</button>
            </gmap-info-window>

        </gmap-marker>
        </gmap-map>
    </div>

    <!-- Sidebar containing visited places and functionalities -->
    <div class="col-sm-3 col-md-3 visited">
        <h1>ToGo List</h1>
            <input type="text" class="form-control" v-model="search" placeholder="Filter Visited Places"></input>
                <ul class="list-group" v-for="(m, index) in filteredPlaces" v-show="m.saved">
                    <li class="list-group-item">{{m.PlaceName}} 
                        <button class="btn btn-danger btn-sm" @click="deletePlace(index)">Delete</button>
                        <button class="btn btn-primary btn-sm" @click="goTo(index)">Go To</button>
                        <button class="btn btn-info btn-sm" @click="mark(index)">{{m.markLabel}}</button>
                    </li>
                </ul>
     </div>
</div>

</template>

<script>
  
  import * as VueGoogleMaps from 'vue2-google-maps';
  import Vue from 'vue';
 
  Vue.use(VueGoogleMaps, {
    load: {
    // Api key you need to change it for your own project
      key: 'AIzaSyCpj3vCNPlhB9gabqQq6dAu4aVC_44N414' 
    }
  });
 
  export default {
    data () {

      return {
        search: "",
        center: {lat: 40.177200, lng: 44.503490},
        markers: []
      }
    },

    methods: {
    
    //Getting map's coordinates
    mapclicked(event) {
   
      const createdPlace = this.addPlace();
      createdPlace.position.lat = event.latLng.lat();
      createdPlace.position.lng = event.latLng.lng();
    
    },
    // Creating markers
    addPlace: function addPlace(name) {
      this.markers.push({
        position: {
          lat: 48.8538302,
          lng: 2.2982161,
        },
        PlaceName: "",
        opacity: 0,
        draggable: false,
        enabled: true,
        dragended: 0,
        ifw: true,
        markLabel: "Mark",
        infoArea: true,
        saved: false
      });
      return this.markers[this.markers.length - 1];  
    },

    //Deleting a place from the map
    deletePlace: function(index){
      this.markers.splice(index, 1);
    },

    //GoTo button functionality
    goTo: function(index){
      if(this.center.lat == this.markers[index].position.lat && this.center.lng == this.markers[index].position.lng){
        this.center = {lat: this.markers[index].position.lat + 0.001, lng: this.markers[index].position.lng + 0.001}
      }else {
        this.center = {lat: this.markers[index].position.lat, lng: this.markers[index].position.lng}
      } 
    },

    //Mark Button functionality
    mark: function(index){
      if(this.markers[index].markLabel == "Mark") {
        this.markers[index].opacity = 1;
        this.markers[index].markLabel = "UnMark";
      }else {
        this.markers[index].opacity = 0;
        this.markers[index].markLabel = "Mark";
      }
    },

    //Saving the place name
    handleInfo: function(index){
      this.markers[index].infoArea = false;
      if(this.$refs.input[index].value.length < 1) {
          alert("Invalid Address Name")
          this.markers.splice(index, 1)
      }else {
          this.markers[index].PlaceName =  this.$refs.input[index].value;
          this.markers[index].saved = true;
      }
    }
  },

  //Filtering Places
  computed: {
    filteredPlaces: function(){
      return this.markers.filter((m) => {
        return m.PlaceName.match(this.search);
      })
    }
  }
    
  }

</script>

<style scoped>
.btn {
  float: right;
  margin-right: 5px;
}

.btn-group-sm>.btn, .btn-sm{
  padding: 1px 10px;
}

.list-group-item{
  padding: 0px;
  margin-bottom: -13px;
  margin-left: 0 px;
}

.form-control {
  margin-bottom: 10px;
}

.label-large {
  vertical-align: super;
  font-size: large;
  margin: 50px;
}

h1{
  text-align: center;
}

.infoWindowButton {
margin-top: 5px;
float: none;
}

</style>
