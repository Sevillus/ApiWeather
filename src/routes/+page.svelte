<script>
	import { onMount } from 'svelte';
	import { writable } from 'svelte/store';

	let places = [];
	let filter = writable('');
	let selectedPlace = writable(null);

	onMount(async () => {
		const url = 'https://danepubliczne.imgw.pl/api/data/synop';

		try {
			const response = await fetch(url);
			if (!response.ok) {
				throw new Error(`HTTP error! status: ${response.status}`);
			}

			const data = await response.json();
			places = data;
		} catch (error) {
			console.error('Wystąpił błąd:', error);
		}
	});

	function handleInput(event) {
		filter.set(event.target.value.toLowerCase());
	}

	function selectPlace(place) {
		selectedPlace.set(place);
	}
</script>

<section style="display: flex; justify-content: center; align-items: center; flex-direction: column">
	<div style="width: 50%">
		<h1>API Wyszukiwarka</h1>
		<input type="text" style="width: 100%; height: 30px; background: none" placeholder="Wyszukaj frazę" on:input={handleInput}>
	</div>
	<div style="margin-top: 3rem">
		{#each $filter ? places.filter(place => place.stacja.toLowerCase().includes($filter)) : places as place}
			<button style="margin: 0.25rem" on:click={() => selectPlace(place)}>
				{place.stacja}
			</button>
		{/each}
	</div>
	{#if $selectedPlace}
		<div style="margin-top: 2rem; border: 1px solid #ccc; padding: 1rem; width: 50%">
			<h2 style="text-align: center; font-weight: 700; font-size: 2rem">{$selectedPlace.stacja}</h2>
			<p>Data pomiaru: {$selectedPlace.data_pomiaru}</p>
			<p>Godzina pomiaru: {$selectedPlace.godzina_pomiaru}</p>
			<p>Temperatura: {$selectedPlace.temperatura} °C</p>
			<p>Ciśnienie: {$selectedPlace.cisnienie} hPa</p>
			<p>Wilgotność względna: {$selectedPlace.wilgotnosc_wzgledna} %</p>
			<p>Kierunek wiatru: {$selectedPlace.kierunek_wiatru} °</p>
			<p>Prędkość wiatru: {$selectedPlace.predkosc_wiatru} m/s</p>
			<p>Suma opadu: {$selectedPlace.suma_opadu} mm</p>
		</div>
	{/if}
</section>

<style>
	button {
		padding: 0.25rem;
		background: #94bee5;
		color: white;
		border: none;
		cursor: pointer;
	}

	input {
		padding: 0.5rem;
		border: 1px solid #ccc;
		border-radius: 4px;
	}

	div {
		margin: 0.25rem 0;
	}
</style>
