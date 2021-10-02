<script context="module" lang="ts">
	export const prerender = true;
</script>

<div id="map" style="height: 500px"></div>

<svelte:head>
	<title>Home</title>
</svelte:head>

<script lang="ts">
	import maplibregl from "maplibre-gl";
	import { onMount } from 'svelte';
	import 'maplibre-gl/dist/maplibre-gl.css';
	
	const mapOptions = {
		center: [-122.483696, 37.833818],
		container: 'map',
		style: 'https://api.maptiler.com/maps/streets/style.json?key=get_your_own_OpIi9ZULNHzrESv6T2vL',
		zoom: 14
	}

	const data = {
		'type': 'Feature',
		'properties': {},
		'geometry': {
			'type': 'LineString',
			'coordinates': [
				// Example trail
				[-122.483696, 37.833818],
				[-122.483482, 37.833174],
				[-122.483396, 37.8327],
				[-122.483568, 37.832056],
				[-122.48404, 37.831141],
				[-122.48404, 37.830497],
				[-122.483482, 37.82992],
				[-122.483568, 37.829548],
				[-122.48507, 37.829446],
			]
		}
	}

	const source = {
		'data': data,
		'lineMetrics': true,
		'type': 'geojson',
		'tolerance': 0.00001,
	}

	const layer = {
		'id': 'route',
		'type': 'line',
		'source': 'route',
		'layout': {
			'line-join': 'round',
			'line-cap': 'round'
		},
		'paint': {
			'line-color': '#888',
			'line-width': 8,
			'line-blur': 2,
			'line-gradient': [
				'interpolate',
				['linear'],
				['line-progress'],
				0,
				'#0298ff',
				1,
				'transparent'
			]
		}
	}

	onMount(async () => {
		var map = new maplibregl.Map(mapOptions);
		console.log('1')
		map.on('load', () => {
			console.log('2')
			map.addSource('route', source);
			map.addLayer(layer);
			const trailPoints = [];

			const updateTrail = () => {
				console.log("3")
				navigator.geolocation.getCurrentPosition((position) => {
					console.log('position coords = ', position.coords)
					
					trailPoints.push([position.coords.longitude, position.coords.latitude]);
					if (trailPoints.length > 10) {
						trailPoints.shift();
					}

					map.getSource('route').setData({
						'type': 'Feature',
						'properties': {},
						'geometry': {
							'type': 'LineString',
							'coordinates': trailPoints
						}
					});

					map.flyTo({center: [position.coords.longitude, position.coords.latitude]})
				}) 
			};

			setInterval(() => updateTrail(), 5000);
		})
	});
</script>