<script>
	import { onMount, setContext } from 'svelte';
	import { mapbox, key } from './mapbox.js';
    import Layercontrol from './Layercontrol.svelte';
	// import mapbox css
	import 'mapbox-gl/dist/mapbox-gl.css';
	setContext(key, {
		getMap: () => map
	});
    
	let container;
	let map;
	onMount(() => {
		
            map = new mapbox.Map({
                container,
                center: [7.75, 48.1],
                zoom:  2.5,
                minZoom: 0,
                maxZoom: 10,
                    // maxBounds: bounds,
                style: {
                        version: 8,
                        sources: {
                            tiles: {
                                type: 'raster',
                            tiles: [
                                "https://tileserver-kaldera.herokuapp.com/services/natural_earth_cross_blended_hypso_shaded_relief.raster/tiles/{z}/{x}/{y}.webp"
                            ],
                            tileSize: 256
                            }
                        },
                        layers: [
                        {
                            id: 'tiles',
                            type: 'raster',
                            source: 'tiles'
                        }
                    ]
                }
            });
			
	});
</script>

<div bind:this={container}>
	{#if map}
        <Layercontrol />
	{/if}
</div>

<style>
	div {
		width: 100%;
		height: 100%;
	}
</style>