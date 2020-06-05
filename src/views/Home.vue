<template>
    <div class="home">
        <h1>Trying to follow you </h1>
        <div v-if="useGmaps" id="map" class="map google-map" ref="googleMap"></div>
        <div id="openlayers-map" class="map">
            <vl-map :load-tiles-while-animating="true" :load-tiles-while-interacting="true"
                data-projection="EPSG:4326" style="height: 400px">
                <vl-view :zoom.sync="openLayers.zoom" :center.sync="openLayers.center" :rotation.sync="openLayers.rotation"></vl-view>
                <vl-feature id="point" :properties="{prop: 'value', prop2: 'value'}" ref="openLayersMarker">
                    <vl-geom-point :coordinates="openLayers.position"></vl-geom-point>
                    <vl-style-box>
                        <vl-style-icon class="map-marker" src="/icons/pin64.png" :anchor="[0.6, 0.9]"></vl-style-icon>

                        <!-- <vl-style-circle :radius="10"> -->
                        <!--     <vl-style-fill color="red"></vl-style-fill> -->
                        <!--     <vl-style-stroke color="red"></vl-style-stroke> -->
                        <!-- </vl-style-circle> -->
                    </vl-style-box>
                </vl-feature>

                <vl-layer-tile id="osm">
                    <vl-source-osm></vl-source-osm>
                </vl-layer-tile>
            </vl-map>
        </div>
    </div>
</template>

<script>
// @ is an alias to /src
	// import HelloWorld from '@/components/HelloWorld.vue'
import GoogleMapsApiLoader from 'google-maps-api-loader'

export default {
    name: 'Home',
    components: {
    },
    props: {
        useGmaps: Boolean,
    },
    data() {
        return {
            openLayers: {
                zoom: 18,
                center: [-8.556399020728977, 41.097856487195514],
                position: [-8.556399020728977, 41.097856487195514],
                rotation: 0,
                geolocPosition: undefined,
            },
            mapConfig: Object,
            apiKey: String,
            google: null,
            map: null,
            markers: [],
            initialPosition: {lat: 59.325, lng: 18.069},
            currentPosition: {lat: 0, lng: 0},
            updateLocationTimer: null,
        }
    },
    async mounted() {
        if (this.useGmaps) {
            this.initializeGmapsVariables();
            const googleMapApi = await GoogleMapsApiLoader({
                apiKey: this.apiKey
            })
            this.google = googleMapApi;
            this.initializeMap();
        }
        this.getUserPosition();
    },

    methods: {
        // Get proper error message based on the code.
        getPositionErrorMessage (code) {
            switch (code) {
                case 1:
                    return 'Permission denied.';
                case 2:
                    return 'Position unavailable.';
                case 3:
                    return 'Timeout reached.';
            }
        },
        initializeGmapsVariables() {
            this.apiKey = 'AIzaSyCt-iMdGMO0i3kSX55NpVfA3ib2C4CVSEw'
            this.mapConfig = {
                center: this.initialPosition,
                zoom: 18,
            };
        },
        initializeMap() {
            const mapContainer = this.$refs.googleMap
            this.map = new this.google.maps.Map(
                mapContainer, this.mapConfig
            );
            this.markers = new this.google.maps.Marker({ 
                map: this.map, 
                position: this.initialPosition
            });
        },

        initializeOpenStreeMaps() {

        },

        moveMap() {
            // Set marker's position.
            this.markers.setPosition(this.currentPosition);

            // Center map to user's position.
            this.map.panTo(this.currentPosition);

        },
        getUserPosition() {
            // Get user's location
            const options = {
                enableHighAccuracy: true,
                maximumAge: 10000,
                //timeout: 120000
            };
            if ('geolocation' in navigator) {
                navigator.geolocation.watchPosition(
                    position => {
                        this.currentPosition.lat = position.coords.latitude;
                        this.currentPosition.lng = position.coords.longitude;
                        this.openLayers.center = [position.coords.longitude, position.coords.latitude];
                        this.openLayers.position = [position.coords.longitude, position.coords.latitude];
                        console.log(`Lat: ${position.coords.latitude} Lng: ${position.coords.longitude}`)
                        if (this.useGmaps) {                        
                            this.moveMap();
                        }
                    },
                    err => alert(`Error (${err.code}): ${this.getPositionErrorMessage(err.code)}`),
                    options
                );
            } else {
                alert('Geolocation is not supported by your browser.');
            }
        }

    }
}
</script>

<style lang="scss">
h1 {
    margin: 1rem;
}

.map-marker {
    height: 16px;
}
</style>
