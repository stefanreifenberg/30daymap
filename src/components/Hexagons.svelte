<script>
	import { onMount } from 'svelte';
	import { maplibre } from './maplibre.js';
	import 'maplibre-gl/dist/maplibre-gl.css';

	let container;
	let map;
    const colors = ['#feebe2','#fcc5c0','#fa9fb5','#f768a1','#dd3497','#ae017e','#7a0177']
	const layers = ['1','2','3','4','5','6','7'];

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
			map.addSource('hexGrid', {
                type: 'geojson',
                data: 'jugend_hex.json'
                });

                map.addLayer({
                id: 'youthHexGrid',
                type: 'fill',
                source: 'hexGrid',
                layout: {},
                paint: {
                    'fill-color': {
                    property: 'bin',
                    stops: colors.map((d, i) => [i, d]),
					
                    },
                    'fill-opacity': 0.7
                }
            });
			
			// Create a popup, but don't add it to the map yet.
            const popup = new maplibre.Popup({
                closeButton: false,
                closeOnClick: false
            });
            
            map.on('mousemove', 'youthHexGrid', (e) => {
            // Change the cursor style as a UI indicator.
                map.getCanvas().style.cursor = 'pointer';
            
            // Copy coordinates array.
                const coordinates = e.lngLat.wrap();
                const description = e.features[0].properties.count;
            
            // Populate the popup and set its coordinates
            // based on the feature found.
                popup.setLngLat(coordinates).setHTML('<h2> No. of facilities: ' + description + '</h2>').addTo(map);
            });
            
            map.on('mouseleave', 'youthHexGrid', () => {
                map.getCanvas().style.cursor = '';
                popup.remove();
            });    

			// create legend
			const legend = document.getElementById('legend');

			function addAlpha(color, opacity) {
				// coerce values so ti is between 0 and 1.
				var _opacity = Math.round(Math.min(Math.max(opacity || 1, 0), 1) * 255);
				return color + _opacity.toString(16).toUpperCase();
			}

			layers.forEach((layer, i) => {
				const color = colors[i];
				const item = document.createElement('div');
				const key = document.createElement('span');
				key.className = 'legend-key';
				key.style.backgroundColor = addAlpha(color, 0.7);

				const value = document.createElement('span');
				value.innerHTML = `${layer}`;
				item.appendChild(key);
				item.appendChild(value);
				legend.appendChild(item);
			});

		});
		
	});
	
</script>

<div id="map" bind:this={container}></div>

<div class="map-overlay">
	<div class="map-overlay-inner">
        <h1>Density of institutions for child and youth work in Freiburg</h1>
        <p>
            Freiburg has a lot to offer for young families and children. 
			I looked at the density of child and youth work in Freiburg to find where most of the institutions are situated.
			Darker colors represent higher densities.
        </p>
		<p style="text-align: center;">Number of facilities</p>
		<div id='legend'></div>
		<p>
			Author: kaldera // Stefan <br>
			<a href="https://github.com/stefanreifenberg/30daymap">Code can be found here</a>
		</p>
		<p>
			Inspired by: <br>
			<a href="https://observablehq.com/@clhenrick/mapboxgl-hexbin-map">Chris Henrick</a>
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
        text-align: left;
	}
 
	.map-overlay h1 {
		line-height: 24px;
		display: inline-block;
		margin: 0 0 10px;
	}
	#legend {
		display: flex;
   		flex-direction: row;
		margin: 0 auto;
		font-size: 25px;
		padding: 10px;
		background-color: #fff;
		box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
		line-height: 18px;
		margin-bottom: 40px;
		width: 200px;
		z-index: 10;
	}

	:global(.legend-key) {
		display: inline-block;
		border-radius: 20%;
		width: 25px;
		height: 25px;
		margin-right: 5px;
		margin-bottom: 10px;
	}
</style>