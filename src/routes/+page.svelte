<script>
	import '../styles/global.css';
	import { onMount } from 'svelte';
	import Nav from './../components/Nav.svelte';
	import mapboxGl, { Map } from 'mapbox-gl';
	import 'mapbox-gl/dist/mapbox-gl.css';
	// import 'mapbox-gl-directions/dist/mapbox-gl-directions.css';
	// import MapboxDirections from '@mapbox/mapbox-gl-directions';

	let map;
	let mapContainer;
	let lng, lat, zoom;

	lng = -71.224518;
	lat = 42.213995;
	zoom = 15;

	onMount(() => {
		const initialState = { lng: lng, lat: lat, zoom: zoom };

		map = new Map({
			container: mapContainer,
			accessToken: 'TOKEN HERE',
			style: `mapbox://styles/mapbox/outdoors-v11`,
			center: [initialState.lng, initialState.lat],
			zoom: initialState.zoom
		});

		map.addControl(new mapboxGl.NavigationControl(), 'top-right');

		map.addControl(
			new MapboxDirections({
				accessToken: 'TOKEN HERE'
			}),
			'top-left'
		);

		// Find current location
		if (navigator.geolocation) {
			navigator.geolocation.getCurrentPosition(
				(position) => {
					const { longitude, latitude } = position.coords;
					console.log('Current Location:', { longitude, latitude });
					map.flyTo({ center: [longitude, latitude], zoom: 12 });
				},
				(error) => {
					console.error('Error getting current location:', error);
				},
				{ enableHighAccuracy: true }
			);
		} else {
			console.error('Geolocation is not supported by your browser');
		}

		// 	map.on('move', () => {
		// 		updateData();
		// 	});
	});

	// onDestroy(() => {
	// 	map.remove();
	// });
</script>

<Nav />

<div class="map" bind:this={mapContainer} />

<style>
	.map {
		height: 100vh;
		width: 100vw;
	}
</style>
