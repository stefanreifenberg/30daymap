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
            style: 'mapbox://styles/yardy/ckvtjmw2p0r9v14nms2npjab8',
            center: [7.848, 47.99],
            zoom: 14.5,
            pitch: 45,
            bearing: -17.6,
            container,
            antialias: true
        });
			
	});
</script>

<div bind:this={container}>
	
</div>

<style>
	div {
		width: 100%;
		height: 100%;
	}
</style>