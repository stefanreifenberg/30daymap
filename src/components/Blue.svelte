<script>
	import { onMount } from 'svelte';
	import { maplibre } from './maplibre.js';
	import 'maplibre-gl/dist/maplibre-gl.css';

	let container;
	let map;

	onMount(() => {

		const map = new maplibre.Map({
			container: 'map',
			style: {
				'version': 8,
				'sources': {
				'raster-tiles': {
				'type': 'raster',
				'tiles': [
					'https://basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
			],
			'tileSize': 256,
			'attribution': '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>'
			
			}
			},
			'layers': [
                {
                'id': 'simple-tiles',
                'type': 'raster',
                'source': 'raster-tiles',
                'minzoom': 0,
                'maxzoom': 22,
                "paint" : {
                        "raster-opacity" : 1
                    }
                }
			]
			},
                center: [ 7.82, 47.995], // starting position
                zoom: 12 // starting zoom
			});
		
		map.on('load', () => {
			map.addSource('water', {
			    type: 'geojson',
			    data: 'freiburg_osm_water_clean.json'
			});

            map.addSource('stadtgrenzen', {
			    type: 'geojson',
			    data: 'stadtteile.json'
			});
			
			map.addLayer({
				'id': 'water-layer',
				'type': 'fill',
				'source': 'water',
				'layout': {},
                'paint': {
                    'fill-color': '#1c29ba', 
                    'fill-opacity': 1
                }
			});
            map.addLayer({
                'id': 'outline',
                'type': 'line',
                'source': 'stadtgrenzen',
                'layout': {},
                'paint': {
                    'line-color': '#000',
                    'line-width': 3,
                    'line-opacity': 0.2
                }
            });
            // Create our popup
            const popup = new maplibre.Popup({
                closeButton: false,
                closeOnClick: false
            });
            
			// Add a mousemove event to the map
            map.on('mousemove', 'water-layer', (e) => {
            	// Change the cursor style as a UI indicator.
                map.getCanvas().style.cursor = 'pointer';
            
            	// Get the mouse coordinates
                const coordinates = e.lngLat.wrap();
				// Get the feature from the layer
                const description = e.features[0].properties.name;
            
            // Populate the popup and set its coordinates
            // based on the feature found.
                popup.setLngLat(coordinates).setHTML('<h2>' + description + '</h2>').addTo(map);
            });

            // Remove everything on mouseleave
            map.on('mouseleave', 'water-layer', () => {
                map.getCanvas().style.cursor = '';
                popup.remove();
            });  
             

			});
	});
</script>

<div id="map" bind:this={container}></div>

<div class="map-overlay">
	<div class="map-overlay-inner">
        <h1 class="colorh1">Lakes</h1><h1>&nbsp;and the</h1><h1 class="colorh1">&nbsp;Dreisam</h1>
        <p>
            Freiburg offers a couple artifical lakes, which are located in the city area. The biggest one being the Opfinger lake which is espacially poupular in the summer.
            The main river of the city is called the "Dreisam" river, named after the celtic word tragisamā or the fast one which also feeds the "Bächle" which we already mapped on Day2.
        </p>
		<p>
			<a href="https://www.openstreetmap.org/#map=12/47.9722/7.7830">Data: OpenStreetMap</a>
		</p>
		<p>
			Author: kaldera // Stefan <br>
			<a href="https://github.com/stefanreifenberg/30daymap">Code can be found here</a>
		</p>
	</div>
</div>

<style>
	#map {
		height: 99vh;
	}
	.map-overlay {
		font: 12px/20px 'Helvetica Neue', Arial, Helvetica, sans-serif;
		position: absolute;
		width: 450px;
		height: fit-content;
		top: 0;
		left: 0;
		padding: 10px;
	}
 
	.map-overlay .map-overlay-inner {
		background-color: #fff;
		box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
		border-radius: 3px;
		padding: 10px;
		margin-bottom: 10px;
        text-align: center;
	}
	.map-overlay h1 {
		line-height: 24px;
		display: inline-block;
		margin: 0 0 10px;
	}
	.colorh1 {
		color: #1c29ba;
	}
</style>