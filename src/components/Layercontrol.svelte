<script>
    import { getContext, onMount } from 'svelte';
    import { key } from './mapbox.js';
    const { getMap } = getContext(key);
	const map = getMap();
    let layerURL = "https://tileserver-kaldera.herokuapp.com/services/natural_earth_cross_blended_hypso_shaded_relief.raster/tiles/{z}/{x}/{y}.webp";

    onMount (() => {
        const layerList = document.getElementById('menu');
        const inputs = layerList.getElementsByTagName('input');

        for (const input of inputs) {
            input.onclick = (layer) => {
                const layerValue = layer.target.value;
                map.getSource('tiles').tiles = [ `${layerValue}?dt=${Date.now()}` ]
                map.style._sourceCaches['other:tiles'].clearTiles();
                map.style._sourceCaches['other:tiles'].update(map.transform)
                map.triggerRepaint()
            };
        }
    });
    
</script>

<style>
    #menu {
        position: absolute;
        background: #ffffff;
        padding: 10px;
        font-family: 'Open Sans', sans-serif;
        z-index: 10;
        font-size: 25px;
    }

</style>

<div id="menu">
    <label for="satellite-v9">
        <input type=radio 
        id="satellite-v9" value={"https://tileserver-kaldera.herokuapp.com/services/natural_earth_cross_blended_hypso_shaded_relief.raster/tiles/{z}/{x}/{y}.webp"} bind:group={layerURL}>
        satellite
    </label>

    <label for="light-v10">
        <input type=radio
        id="light-v10" value={"https://tileserver-kaldera.herokuapp.com/services/natural_earth_gray_earth_hypso_shaded_relief.raster/tiles/{z}/{x}/{y}.webp"} bind:group={layerURL}>
        light
    </label>
   
</div>
