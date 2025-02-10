# Map-of-California
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Proportional Symbols Map</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <style>
        #map {
            width: 100%;
            height: 500px;
        }
        .legend {
            background-color: white;
            padding: 10px;
            border: 1px solid #ccc;
            margin-top: 10px;
        }
    </style>
</head>
<body>

    <h1>Proportional Symbols Map: Population by City</h1>
    <div id="map"></div>

    <!-- Legend -->
    <div id="legend" class="legend"></div>

    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <script>
        // Initialize the map
        var map = L.map('map').setView([37.5, -122.5], 5);  // Coordinates for initial map view (example: California)

        // Add a tile layer (this is a base map, you can change the style here)
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        // Sample GeoJSON data
        var geojsonData = {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "geometry": {
                        "type": "Point",
                        "coordinates": [-122.4194, 37.7749]  // San Francisco
                    },
                    "properties": {
                        "city": "San Francisco",
                        "population": 883305
                    }
                },
                {
                    "type": "Feature",
                    "geometry": {
                        "type": "Point",
                        "coordinates": [-118.2437, 34.0522]  // Los Angeles
                    },
                    "properties": {
                        "city": "Los Angeles",
                        "population": 3990456
                    }
                },
                {
                    "type": "Feature",
                    "geometry": {
                        "type": "Point",
                        "coordinates": [-73.935242, 40.730610]  // New York City
                    },
                    "properties": {
                        "city": "New York",
                        "population": 8175133
                    }
                }
            ]
        };

        // Function to calculate the radius based on population size
        function getSymbolSize(population) {
            return Math.sqrt(population) * 0.0002;  // Adjust factor to scale symbol size
        }

        // Adding GeoJSON layer with proportional symbols
        L.geoJSON(geojsonData, {
            pointToLayer: function (feature, latlng) {
                // Use population to set proportional symbol size
                var radius = getSymbolSize(feature.properties.population);

                return L.circleMarker(latlng, {
                    radius: radius,
                    fillColor: '#ff5733',
                    color: '#ffffff',
                    weight: 1,
                    opacity: 1,
                    fillOpacity: 0.6
                }).bindPopup("<b>City: </b>" + feature.properties.city + "<br><b>Population: </b>" + feature.properties.population);
            }
        }).addTo(map);

        // Creating a legend (bonus point)
        var legend = L.control({position: 'bottomright'});

        legend.onAdd = function() {
            var div = L.DomUtil.create('div', 'legend');
            var grades = [1000000, 2000000, 3000000, 4000000];
            var labels = [];

            // Loop through grades and generate a label with colored square for each
            for (var i = 0; i < grades.length; i++) {
                div.innerHTML +=
                    '<i style="background:' + getColor(grades[i]) + '"></i> ' +
                    grades[i] + (grades[i + 1] ? '&ndash;' + grades[i + 1] + '<br>' : '+');
            }

            return div;
        };

        legend.addTo(map);

        // Function to determine color based on population range
        function getColor(population) {
            return population > 4000000 ? '#ff0000' :
                population > 3000000  ? '#ff6600' :
                population > 2000000  ? '#ff9900' :
                population > 1000000  ? '#ffcc00' :
                                        '#ffff00';
        }
    </script>

</body>
</html>
