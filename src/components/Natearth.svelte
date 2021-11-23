<script>
	import { onMount } from 'svelte';
	import { mapbox, key } from './mapbox.js';
	// import mapbox css
	import 'mapbox-gl/dist/mapbox-gl.css';


	let container;
	let map;

	onMount(() => {
		
        map = new mapbox.Map({
            style: 'mapbox://styles/yardy/ckvxjho1t5yq415o7upzp3qfz',
            center: [7.848, 47.99],
            zoom: 4,
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