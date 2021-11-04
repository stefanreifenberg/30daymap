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
			'maxzoom': 22
			}
			]
			},
			center: [ 7.8, 48], // starting position
			zoom: 12// starting zoom
			});
		
		

            map.on('load', () => {
                // Load an image from an external URL.
                map.loadImage(
                'fire-station.png',
                (error, image) => {
                if (error) throw error;
                
                // Add the image to the map style.
                map.addImage('cat', image);
                
                // Add a data source containing one point feature.
                map.addSource('firestations', {
                    type: 'geojson',
                    data: 'fire.json'
                });
                
                // Add a layer to use the image to represent the data.
                map.addLayer({
                'id': 'firestations-layer',
                'type': 'symbol',
                'source': 'firestations', // reference the data source
                'layout': {
                'icon-image': 'cat', // reference the image
                'icon-size': 0.15
                }
                });
                }
                );
                });
	});
</script>

<div id="map" bind:this={container}></div>

<div class="map-overlay">
	<div class="map-overlay-inner">
		<h1 class="colorh1">Playgrounds</h1><h1>&nbsp;in Freiburg i. Breisgau</h1>
		<p>
			<a href="https://geodaten.freiburg.de/geonetwork/srv/ger/catalog.search#/metadata/cd2b921e-8f87-44dd-acc2-b558f6331b34">Data: Freiburg geodata catalog</a>
		</p>
		<p>
			Author: kaldera // Stefan <br>
			<a href="https://github.com/stefanreifenberg/30daymap">Code can be found here</a>
		</p>
        <div>Icons made by <a href="https://www.freepik.com" title="Freepik">Freepik</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a></div>
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
	}
 
	.map-overlay h1 {
		line-height: 24px;
		display: inline-block;
		margin: 0 0 10px;
	}
	.colorh1 {
		color: #dba512;
	}
</style>