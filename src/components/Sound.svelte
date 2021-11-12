<script>
	import { onMount } from 'svelte';
	import { maplibre } from './maplibre.js';
	//import 'maplibre-gl/dist/maplibre-gl.css';
    import 'maplibre-gl/dist/maplibre-gl.css';
    import '@maplibre/maplibre-gl-compare/dist/maplibre-gl-compare.css';
    import { Compare } from '@maplibre/maplibre-gl-compare/dist/maplibre-gl-compare'

    

    

	let container;
	let map;

	onMount(async () => {
        
       //let Compare = await import('@maplibre/maplibre-gl-compare/dist/maplibre-gl-compare');

        const beforeMap = new maplibre.Map({
            container: 'before',
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
            center: [0, 0],
            zoom: 0
        });
        
        const afterMap = new maplibre.Map({
            container: 'after',
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
            center: [0, 0],
            zoom: 0
        });

        

		
		
		afterMap.on('load', () => {
            const container = "#comparison-container";

                const map = new maplibre.Compare(beforeMap, afterMap, container, {
                // Set this to enable comparing two maps by mouse movement:
                // m ousemove: true
            });
            
            // A selector or reference to HTML element
            
			
		});
	});
</script>

<div id="comparison-container">
    <div id="before" class="map"></div>
    <div id="after" class="map"></div>
</div>

<style>
	.map {
        position: absolute;
        top: 0;
        bottom: 0;
        width: 100%;
    }
</style>