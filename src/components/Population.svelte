<script>
	import { onMount } from 'svelte';
	import { maplibre } from './maplibre.js';
    import { mapbox } from './mapbox.js';
	import 'mapbox-gl/dist/mapbox-gl.css';

	let container;
	let map;

	onMount(() => {

		map = new mapbox.Map({
			    container,
			    style: 'mapbox://styles/yardy/ckvv3iy6e0sfq15qvwc842ypc',
                center: [ 7.82, 47.995],
                zoom: 11
			});
		
		map.on('load', () => {
			map.addSource('bezirke', {
			type: 'geojson',
			data: 'freiburg_einwohner.json'
			});
			
			map.addLayer({
				'id': 'bezirke-layer',
				'type': 'fill',
				'source': 'bezirke',
				'layout': {},
                'paint': {
                    'fill-color': '#0080ff',
                    'fill-opacity': 0
                }
				});

            map.addLayer({
                'id': 'outline',
                'type': 'line',
                'source': 'bezirke',
                'layout': {},
                'paint': {
                    'line-color': '#000',
                    'line-width': 1
                }
                });
            // Create our popup
            const popup = new maplibre.Popup({
                closeButton: false,
                closeOnClick: false
            });
            
			// Add a mousemove event to the map
            map.on('mousemove', 'bezirke-layer', (e) => {
            	// Change the cursor style as a UI indicator.
                map.getCanvas().style.cursor = 'pointer';
            
            	// Get the mouse coordinates
                const coordinates = e.lngLat.wrap();
				// Get the feature from the layer
                const description = e.features[0].properties.name;
                const pop = e.features[0].properties.einwohner;
            
            // Populate the popup and set its coordinates
            // based on the feature found.
                popup.setLngLat(coordinates).setHTML('<h2>' + description + '</h2>' + '<h3>Population: ' + pop + '</h3>').addTo(map);
            });

            // Remove everything on mouseleave
            map.on('mouseleave', 'bezirke-layer', () => {
                map.getCanvas().style.cursor = '';
                popup.remove();
            });    

			});
	});
</script>

<div id="map" bind:this={container}></div>

<div class="map-overlay">
	<div class="map-overlay-inner">
        <h1>Population per district in Freiburg (2020)</h1>
        <h2>
            Total population: <strong>229425</strong>
        </h2>
		<p>
			<a href="https://fritz.freiburg.de/Informationsportal/#app/mainpage//Einwohner">Data: Freiburg open data portal</a>
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
</style>