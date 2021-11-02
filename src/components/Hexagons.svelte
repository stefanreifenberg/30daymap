<script>
	import { onMount } from 'svelte';
	import { maplibre } from './maplibre.js';
	import 'maplibre-gl/dist/maplibre-gl.css';

	let container;
	let map;
    const colorRamp = ['#feebe2','#fcc5c0','#fa9fb5','#f768a1','#dd3497','#ae017e','#7a0177']

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
                zoom: 11 // starting zoom
			});
		
		map.on('load', () => {
			map.addSource('hexGrid', {
                type: 'geojson',
                data: 'jugend_hex.json'
                });

                map.addLayer({
                id: 'crashesHexGrid',
                type: 'fill',
                source: 'hexGrid',
                layout: {},
                paint: {
                    'fill-color': {
                    property: 'bin',
                    stops: colorRamp.map((d, i) => [i, d])
                    },
                    'fill-opacity': 0.7
                }
            });

		});
	});
</script>

<div id="map" bind:this={container}></div>

<div class="map-overlay">
	<div class="map-overlay-inner">
        <h1>Districts of Freiburg im Breisgau</h1>
        <p>
            The city of Freiburg im Breisgau is divided into 28 districts. The formerly independent communities of Ebnet im Dreisamtal, Hochdorf im Breisgau, Kappel im Tal, Lehen im Breisgau, Munzingen, Opfingen, Tiengen am Tuniberg and Waltershofen, which were recently incorporated as part of the district reform in Baden-Württemberg, were introduced to a local constitution.
        </p>
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
</style>