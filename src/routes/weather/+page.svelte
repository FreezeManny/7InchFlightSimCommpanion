<script lang="ts">
	import { settings } from '$lib/settings';
	import { onMount } from 'svelte';
	import { onDestroy } from 'svelte';

	import { localStorageStore } from '@skeletonlabs/skeleton';
	import type { Writable } from 'svelte/store';
	import { get } from 'svelte/store';

	import { EventEmitter } from 'events';

	class VariableWatcher extends EventEmitter {
		private _variable: any = null;

		set var(value: any) {
			this._variable = value;
			this.emit('change', value);
		}

		get var(): any {
			return this._variable;
		}
	}

	// Change the variables

	const VATSIMDATAURL = 'https://data.vatsim.net/v3/vatsim-data.json';
	const VATSIM_METAR_URL = 'https://metar.vatsim.net/';

	var dep = {
		heading: 'Departure Airport: XXXX',
		input: '',
		atisCode: '',
		atisText: '',
		metar: ''
	};

	var arr = {
		heading: 'Arrival Airport: XXXX',
		input: '',
		atisCode: '',
		atisText: '',
		metar: ''
	};

	enum fetchMode {
		DEP,
		ARR
	}

	// Create an instance of VariableWatcher
	const depAptWatcher = new VariableWatcher();
	const arrAptWatcher = new VariableWatcher();

	// Listen for the 'change' event
	depAptWatcher.on('change', (val: any) => {
		dep.input = val;
		console.log('Departure Airport changed:', val);
		dep.heading = 'Departure Airport: ' + val;
		fetchATIS(fetchMode.DEP);
		fetchMETAR(fetchMode.DEP);
	});
	arrAptWatcher.on('change', (val: any) => {
		console.log('Arrival Airport changed:', val);
		arr.input = val;
		arr.heading = 'Arrival Airport: ' + val;
		fetchATIS(fetchMode.ARR);
		fetchMETAR(fetchMode.ARR);
	});

	async function fetchSimbriefRoute() {
		console.log('');

		let data: any;
		try {
			const response = await fetch('https://www.simbrief.com/api/xml.fetcher.php?username=' + settings.simbriefUsername + '&json=1');
			data = await response.json();
			//console.log(data);
			depAptWatcher.var = data.origin.icao_code;
			arrAptWatcher.var = data.destination.icao_code;
		} catch (error) {
			console.error('Failed to fetch data:', error);
		}
	}

	async function fetchMETAR(mode: fetchMode) {
		try {
			console.log('fetchMetar');
			var tmpAirport = '';
			var tmpATIS = '';
			var tmpAtisCode = '';
			//console.log(data);
			var atisList: any = [];

			if (mode == fetchMode.DEP) tmpAirport = depAptWatcher.var.toUpperCase();
			else if (mode == fetchMode.ARR) tmpAirport = arrAptWatcher.var.toUpperCase();
			const response = await fetch(VATSIM_METAR_URL + tmpAirport);
			if (!response.ok) {
				throw new Error('Network response was not ok');
			}
			const data = await response.text();
			console.log(data);
			if (mode == fetchMode.DEP) dep.metar = data;
			else if (mode == fetchMode.ARR) arr.metar = data;
		} catch (error) {
			console.error('There was a problem fetching the data:', error);
		}
	}

	async function fetchATIS(mode: fetchMode) {
		try {
			const response = await fetch(VATSIMDATAURL);
			if (!response.ok) {
				throw new Error('Network response was not ok');
			}
			const data = await response.json();
			const atisData = data.atis;

			var tmpAirport = '';
			var tmpATIS = '';
			var tmpAtisCode = '';
			//console.log(data);
			var atisList: any = [];

			if (mode == fetchMode.DEP) tmpAirport = depAptWatcher.var.toUpperCase();
			else if (mode == fetchMode.ARR) tmpAirport = arrAptWatcher.var.toUpperCase();

			atisData.forEach((element: any) => {
				if (element.callsign.includes(tmpAirport)) {
					console.log(element);
					atisList.push(element);
				}
			});
			console.log(atisList.length);

			if (atisList.length == 1) {
				tmpAtisCode = atisList[0].atis_code;
				tmpATIS = atisList[0].text_atis;
			} else if (atisList.length == 2) {
				tmpATIS += atisList[0].text_atis;
				tmpATIS += '<br><br>';
				tmpATIS += atisList[1].text_atis;
			} else {
				tmpATIS = 'No ATIS Online';
			}

			if (mode == fetchMode.DEP) {
				dep.atisText = tmpATIS;
				dep.atisCode = tmpAtisCode;
			}
			if (mode == fetchMode.ARR) {
				arr.atisText = tmpATIS;
				arr.atisCode = tmpAtisCode;
			}
		} catch (error) {
			console.error('There was a problem fetching the data:', error);
		}
	}

	function depInputHandler() {
		dep.input = dep.input.toUpperCase();

		if (dep.input.length == 4) {
			depAptWatcher.var = dep.input;
		} else if (dep.input.length > 4) {
			dep.input = dep.input.slice(0, 4);
		}
	}

	function arrInputHandler() {
		arr.input = arr.input.toUpperCase();

		if (arr.input.length == 4) {
			arrAptWatcher.var = arr.input;
		} else if (arr.input.length > 4) {
			arr.input = arr.input.slice(0, 4);
		}
	}

	async function simbriefButtonHandler() {
		console.log('Simbrief Button');
		await fetchSimbriefRoute();
	}

	// in your Svelte component
	const depAptSave: Writable<string> = localStorageStore('depAptSave', 'EDDS');
	const arrAptSave: Writable<string> = localStorageStore('arrAptSave', 'EDDS');

	onMount(() => {
		depAptWatcher.var = get(depAptSave);
		arrAptWatcher.var = get(arrAptSave);

		//getting Data
		dep.heading = 'Departure Airport: ' + depAptWatcher.var;
		arr.heading = 'Arrival Airport: ' + arrAptWatcher.var;
	});

	onDestroy(() => {
		// Perform cleanup tasks here
		depAptSave.set(depAptWatcher.var);
		arrAptSave.set(arrAptWatcher.var);
		console.log('Component is being destroyed');
	});
</script>

<div class="grid grid-cols-3 p-2">
	<div class="mx-3">
		<div class="input-group input-group-divider grid-cols-[auto_1fr_auto]">
			<div class="input-group-shim">Dep</div>
			<input type="text" placeholder="EDDS" bind:value={dep.input} on:input={depInputHandler} />
		</div>
	</div>

	<div class="mx-3">
		<div class="input-group input-group-divider grid-cols-[auto_1fr_auto]">
			<div class="input-group-shim">Arr</div>
			<input type="text" placeholder="EDDS" bind:value={arr.input} on:input={arrInputHandler} />
		</div>
	</div>
	<button type="button" class="btn variant-filled mx-3" on:click={simbriefButtonHandler}>Simbrief</button>
</div>

<div class="flex flex-col p-2">
	<div class="card my-1">
		<header class="card-header">{dep.heading}</header>
		<section class="p-4">
			<div class="card mb-2">
				<header class="card-header">ATIS {dep.atisCode}</header>
				<section class="p-4">{@html dep.atisText}</section>
			</div>
			<div class="card">
				<header class="card-header">METAR</header>
				<section class="p-4">{dep.metar}</section>
			</div>
		</section>
	</div>

	<div class="card my-1">
		<header class="card-header">{arr.heading}</header>
		<section class="p-4">
			<div class="card mb-2">
				<header class="card-header">ATIS {arr.atisCode}</header>
				<section class="p-4">{arr.atisText}</section>
			</div>

			<div class="card">
				<header class="card-header">METAR</header>
				<section class="p-4">{arr.metar}</section>
			</div>
		</section>
	</div>
	<!--
  <div class="card">
    <header class="card-header">(header)</header>
    <section class="p-4">(content)</section>
  </div>
  -->
</div>
