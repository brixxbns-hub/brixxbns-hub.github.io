<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>

  <title>Live Location Tracker</title>

  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet/dist/leaflet.css"
  />

  <style>
    body{
      margin:0;
      background:#0f172a;
      color:white;
      font-family:Arial;
      text-align:center;
    }

    .container{
      padding:20px;
    }

    button{
      padding:14px 22px;
      border:none;
      border-radius:12px;
      background:#38bdf8;
      color:white;
      font-size:16px;
      cursor:pointer;
      margin-top:15px;
    }

    #map{
      height:500px;
      width:100%;
      margin-top:20px;
    }

    .info{
      margin-top:15px;
      line-height:1.8;
    }
  </style>
</head>
<body>

  <div class="container">
    <h1>Live Location Tracker</h1>

    <p>
      This website requests permission to access your location.
    </p>

    <button onclick="startTracking()">
      Share My Location
    </button>

    <div class="info" id="info"></div>
  </div>

  <div id="map"></div>

  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

  <script>
    const info = document.getElementById("info");

    const map = L.map('map').setView([0, 0], 2);

    L.tileLayer(
      'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      {
        attribution: '© OpenStreetMap'
      }
    ).addTo(map);

    let marker;

    function startTracking() {

      if (!navigator.geolocation) {
        alert("Geolocation not supported.");
        return;
      }

      info.innerHTML = "Requesting location access...";

      navigator.geolocation.watchPosition(

        (position) => {

          const lat = position.coords.latitude;
          const lng = position.coords.longitude;
          const accuracy = position.coords.accuracy;

          info.innerHTML = `
            Latitude: ${lat}<br>
            Longitude: ${lng}<br>
            Accuracy: ${accuracy} meters
          `;

          map.setView([lat, lng], 16);

          if(marker){
            marker.setLatLng([lat, lng]);
          } else {
            marker = L.marker([lat, lng])
              .addTo(map)
              .bindPopup("Current Location")
              .openPopup();
          }

          // OPTIONAL:
          // Send location to your backend/webhook

          /*
          fetch("YOUR_SERVER_URL", {
            method: "POST",
            headers: {
              "Content-Type": "application/json"
            },
            body: JSON.stringify({
              latitude: lat,
              longitude: lng,
              accuracy: accuracy,
              time: new Date()
            })
          });
          */

        },

        (error) => {

          if(error.code === 1){
            info.innerHTML = "Permission denied.";
          }
          else{
            info.innerHTML = "Unable to retrieve location.";
          }

        },

        {
          enableHighAccuracy: true,
          maximumAge: 0,
          timeout: 10000
        }

      );

    }
  </script>

</body>
</html>
