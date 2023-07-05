<template>
  <div>
    <div id="map" style="width: 100%; height: 400px"></div>
  </div>
</template>

<script>
export default {
  name: "MapView",
  data() {
    return {
      map: null,
      marker: null,
      pathCoordinates: null,
      positionWatcher: null,
      currentPosition: { lat: -25.344, lng: 131.036 }
    };
  },
  methods: {
    initMap() {
      this.map = new google.maps.Map(document.getElementById("map"), {
        zoom: 4,
        center: this.currentPosition,
      });

      this.marker = new google.maps.Marker({
        position: this.currentPosition,
        map: this.map,
      });

      this.pathCoordinates = [
        new google.maps.LatLng(-25.363882, 131.044922),
        new google.maps.LatLng(-27.363882, 133.044922),
      ];

      const pathLine = new google.maps.Polyline({
        path: this.pathCoordinates,
        geodesic: true,
        strokeColor: "#FF0000",
        strokeOpacity: 1.0,
        strokeWeight: 2,
      });

      pathLine.setMap(this.map);
    },
    startWatchingPosition() {
      if (!navigator.geolocation) {
        alert('Geolocation is not supported by your browser');
        return;
      }

      this.positionWatcher = setInterval(() => {
        navigator.geolocation.getCurrentPosition((position) => {
          this.currentPosition = { lat: position.coords.latitude, lng: position.coords.longitude };
          this.marker.setPosition(this.currentPosition);
          this.map.setCenter(this.currentPosition);
        }, () => {
          alert('Unable to retrieve your location');
        });
      }, 2000);
    },
    stopWatchingPosition() {
      if (this.positionWatcher) {
        clearInterval(this.positionWatcher);
        this.positionWatcher = null;
      }
    }
  },
  mounted() {
    if (!window.google) {
      const script = document.createElement('script');
      script.src = `https://maps.googleapis.com/maps/api/js?key=AIzaSyAHlGuFEKxf_fu8QdgeTObUHqkFRLAMfpM`;
      script.async = true;
      script.defer = true;
      document.head.appendChild(script);

      script.onload = () => {
        this.initMap();
        this.startWatchingPosition();
      }
    } else {
      this.initMap();
      this.startWatchingPosition();
    }
  },
  beforeDestroy() {
    this.stopWatchingPosition();
  }
};
</script>
