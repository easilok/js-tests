<template>
    <div class="home">
        <h1>Trying to follow you </h1>
        <div id="map" class="map google-map" ref="googleMap"></div>
    </div>
</template>

<script>
// @ is an alias to /src
	// import HelloWorld from '@/components/HelloWorld.vue'
import GoogleMapsApiLoader from 'google-maps-api-loader'

export default {
    name: 'GMaps',
    components: {
    },
    props: {
    },
    data() {
        return {
            mapConfig: Object,
            apiKey: String,
            google: null,
            map: null,
            markers: [],
            initialPosition: {lat: 59.325, lng: 18.069},
            currentPosition: {lat: 0, lng: 0},
        }
    },
    async mounted() {
        this.initializeGmapsVariables();
        const googleMapApi = await GoogleMapsApiLoader({
            apiKey: this.apiKey
        })
        this.google = googleMapApi;
        this.initializeMap();
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
                        console.log(`Lat: ${position.coords.latitude} Lng: ${position.coords.longitude}`)
                        this.moveMap();
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
</style>
