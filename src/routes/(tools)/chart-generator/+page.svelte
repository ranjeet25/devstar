<script lang="ts">
	import Intro from '$lib/Intro.svelte';
	import LineChart from './LineChart.svelte';
	import BarChart from './BarChart.svelte';

	export let data;
	let opt = 'BarChart'; // Set an initial value for opt.

	function handleChange(event) {
		opt = event.target.value;
	}
</script>

<Intro heading={data.meta.title} description={data.meta.description} />
<div
	class="main-container mb-4 flex justify-center items-center flex-col lg:flex-row py-4 px-12 border-2 rounded-lg shadow-sm shadow-blue-300"
>
	<!-- Input fields to capture user input for each data item -->

<section class="bg-white dark:bg-gray-900" />
<select bind:value={opt} on:change={handleChange}>
	<option value="BarChart">Bar Chart</option>
	<option value="LineChart">Line Chart</option>
</select>
</div>

<div class="flex justify-center items-center">
	{#if opt === 'BarChart'}
		<BarChart />
	{:else if opt === 'LineChart'}
		<LineChart />
	{/if}
</div>

<style>
	.card {
		box-shadow: rgba(0, 0, 0, 0.1) 0 0 0 2px;
	}

	:is(.dark .card) {
		box-shadow: rgba(255, 255, 255, 0.5) 0 0 0 2px;
	}
</style>
