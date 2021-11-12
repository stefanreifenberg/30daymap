<script>
	import { onMount } from 'svelte';
	import { maplibre } from './maplibre.js';
    import { mapbox } from './mapbox.js';
	import 'mapbox-gl/dist/mapbox-gl.css';
    import * as d3 from 'd3';

	let container;
	let map;
    const colors = ['#1d4e89','#606d94','#a48d9e','#e8c9b3','#e9d7b8','#eae2bb']
	const layers = ['45 - 4210','4210 - 8374','8374 - 12539','12539 - 16704','16704 - 20686','20686 - 25033'];

    async function drawBars() {

        const data = await d3.csv('freiburg_einwohner.csv', function(d) {
            return {
                name: d.name,
                value: +d.einwohner
            };
            });

            // sort data 
            data.sort(function(a, b) {
                return b.value - a.value;
            });

        const dimensions = ({  
            height:700,
            width:440,  
            margin: {
                top: 5,
                right: 10,
                bottom: 60,
                left: 75,
                } 
        })
        
        const svg = d3.select("#chart")
        .append("svg")
            .attr("width", dimensions.width + dimensions.margin.left + dimensions.margin.right)
            .attr("height", dimensions.height + dimensions.margin.top + dimensions.margin.bottom)
        .append("g")
            .attr("transform",
                "translate(" + dimensions.margin.left + "," + dimensions.margin.top + ")")       
            
        const xScale = d3.scaleLinear()
            .domain([0, d3.max(data, d => +d.value)])
            .range([0, dimensions.width - dimensions.margin.right])
            .nice()
        
        const xAxisGenerator = d3.axisBottom()
            .scale(xScale)
        
        const xAxis = svg.append("g")
            .call(xAxisGenerator)
            .style("transform", `translateY(${dimensions.height}px)`)

        const y = d3.scaleBand()
            .range([ 0, dimensions.height ])
            .domain(data.map(d => d.name))    
            .padding(.1)
        
        svg.append("g")
            .call(d3.axisLeft(y))
        
        svg.selectAll("myRect")
            .data(data)
            .enter()
            .append("rect")
            .attr("x", xScale(0) )
            .attr("y", d => y(d.name))
            .attr("width", d => xScale(d.value) - xScale(0))
            .attr("height", y.bandwidth())
            .attr("fill", "#8a89a6")
    }

	onMount(() => {
        // draw barchart
        drawBars();

		map = new mapbox.Map({
			    container,
			    style: 'mapbox://styles/yardy/ckvv3iy6e0sfq15qvwc842ypc',
                center: [ 7.73, 47.995],
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
            // create legend
			const legend = document.getElementById('legend');

            layers.forEach((layer, i) => {
                const color = colors[i];
                const item = document.createElement('div');
                const key = document.createElement('span');
                key.className = 'legend-key';
                key.style.backgroundColor = color;

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
<div id='legend'>
    <h3>Population</h3>
</div>

<div class="map-overlay">
	<div class="map-overlay-inner">
        <h1>Population per district in Freiburg (2020)</h1>
        <h2>
            Total: <strong>229425</strong> inhabitants
        </h2>
        
        <div id='chart'></div>
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
		width: 550px;
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
    #legend {
        position: absolute;
        top: 0;
        right: 0;
		display: flex;
   		flex-direction: column;
        align-items: flex-start;
		margin: 0 auto;
		font-size: 20px;
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
        vertical-align: text-top;
		border-radius: 20%;
		width: 25px;
		height: 25px;
		margin-right: 5px;
		margin-bottom: 10px;
	}
</style>