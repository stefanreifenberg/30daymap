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
                center: [ 7.85, 47.995], // starting position
                zoom: 16 // starting zoom
			});
		
		map.on('load', () => {
			map.addSource('baechle', {
			type: 'geojson',
			data: 'baechle.json'
			});
			
			map.addLayer({
				'id': 'baechle-layer',
				'type': 'line',
				'source': 'baechle',
				'layout': {
                    'line-join': 'round',
                    'line-cap': 'round'
                    },
                    'paint': {
                    'line-color': '#99b9f0',
                    'line-width': 5
                }
				});
			});
	});
</script>

<div id="map" bind:this={container}></div>

<div class="map-overlay">
	<div class="map-overlay-inner">
        <h1>Freiburger</h1><h1 class="colorh1">&nbsp;Bächle</h1>
        <p>
            “If visiting Freiburg for the first time, you will be pleasantly surprised by the many open streams flowing through the streets with crystal clear water.” (Description of the city from 1896)
        </p>
        <div id="picture">
            <a title="Rob Faulkner from Leeds, United Kingdom, CC BY 2.0 &lt;https://creativecommons.org/licenses/by/2.0&gt;, via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:B%C3%A4chle_(6221487826).jpg"><img width="256" alt="Bächle (6221487826)" src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/69/B%C3%A4chle_%286221487826%29.jpg/256px-B%C3%A4chle_%286221487826%29.jpg"></a>
            
        </div>
        <a href="https://commons.wikimedia.org/wiki/File:B%C3%A4chle_(6221487826).jpg">Rob Faulkner from Leeds, United Kingdom</a>, <a href="https://creativecommons.org/licenses/by/2.0">CC BY 2.0</a>, via Wikimedia Commons
		<p>
			<a href="https://geodaten.freiburg.de/geonetwork/srv/ger/catalog.search#/metadata/89141cd9-e688-4c7c-ba85-fe4ea776984d">Data: Freiburg geodata catalog</a>
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
		color: #99b9f0;
	}
    #picture {
        display: flex;
        justify-content: center;
    }
</style>