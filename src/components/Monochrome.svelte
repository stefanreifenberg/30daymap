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
					'https://stamen-tiles.a.ssl.fastly.net/toner/{z}/{x}/{y}.png',
			],
			'tileSize': 256,
			'attribution': 'Map tiles by <a href="http://stamen.com">Stamen Design</a>, under <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a>. Data by <a href="http://openstreetmap.org">OpenStreetMap</a>, under <a href="http://www.openstreetmap.org/copyright">ODbL</a>.'
			
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
                zoom: 13 // starting zoom
			});
		
		map.on('load', () => {
			map.addSource('buildings', {
			type: 'geojson',
			data: 'freiburg_osm_buildings.json'
			});
			
			map.addLayer({
				'id': 'buildings-layer',
				'type': 'fill',
				'source': 'buildings',
				'layout': {},
                'paint': {
                    'fill-color': 'black', 
                    'fill-opacity': 1
                }
			});

            // Create our popup
            const popup = new maplibre.Popup({
                closeButton: false,
                closeOnClick: false
            });
            
			// Add a mousemove event to the map
            map.on('mousemove', 'buildings-layer', (e) => {
            	// Change the cursor style as a UI indicator.
                map.getCanvas().style.cursor = 'pointer';
            
            	// Get the mouse coordinates
                const coordinates = e.lngLat.wrap();
				// Get the feature from the layer
                const description = e.features[0].properties.name;
                const type = e.features[0].properties.type;
            
            // Populate the popup and set its coordinates
            // based on the feature found.
                if (description) {
                    popup.setLngLat(coordinates).setHTML('<h2>' + description + '</h2>').addTo(map);
                }
            });

            // Remove everything on mouseleave
            map.on('mouseleave', 'buildings-layer', () => {
                map.getCanvas().style.cursor = '';
                popup.remove();
            });  

			});
	});
</script>

<div id="map" bind:this={container}></div>

<div class="map-overlay">
	<div class="map-overlay-inner">
        <h1>Buildings in Freiburg i. Breisgau</h1>
        
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
		color: #397d4a;
	}
</style>