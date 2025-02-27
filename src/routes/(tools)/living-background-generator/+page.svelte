<script lang="ts">
	import Intro from '$lib/Intro.svelte';
	import { Label, Input, Range, Radio, Checkbox } from 'flowbite-svelte';
	import { onMount } from 'svelte';
	import Copy from '$lib/Copy.svelte';

	export let data;
	let innerWidth: any = null;
	let innerHeight: any = null;
	let screenWidth: any = null;
	let screenHeight: any = null;
	let css = '';

	onMount(() => {
		screenWidth = screen.width;
		screenHeight = screen.height;
		colorInput = document.getElementById('color-input');
		alphaSlider = document.getElementById('alpha-slider');
		colorDisplay = document.getElementById('color-display');
	});

	let clrList = ['#000000', '#bb2d6f', '#fd9d1d', '#fcf437'];
	let clrVal3 = '#000';
	let isRandom = false; // A flag to toggle random colors

	const pushArr = () => {
		clrList.length < 6 ? (clrList = [...clrList, clrVal3]) : alert('Too many colors');
	};

	//remove the div on click

	const removeOnClick = (e) => {
		clrList = clrList.filter((x, y) => x !== e.target.id);
		console.log(e.target.id);
	};

	$: clrStyle = clrList.map((x) => {
		return `background: ${x};`;
	});

	let clrVal = '';

	const updateClrVal1 = () => {
		clrVal = '#0f0';
	};
	const updateClrVal2 = () => {
		clrVal = '#00f';
	};

	$: bgGradient =
		clrList.length > 1
			? `background-image: linear-gradient(${angle}deg, ${clrList});`
			: `background-color: ${clrList};`;

	let angle = 270;

	let speed = 7;

	$: gradientSpeed = `animation-duration: ${speed}s;`;

	let colorInput;
	let alphaSlider;
	let colorDisplay;

	const updateColorDisplay = () => {
		const selectedColor = colorInput.value;
		const alphaValue = alphaSlider.value;
		colorDisplay.style.backgroundColor = selectedColor;
		colorDisplay.style.opacity = alphaValue;
	};

	let myBtn;

	// Set the initial color

	$: css = `
	.animated-background {
		${bgGradient}
		background-size: 400% 400%;
		animation: gradient ${speed}s ease infinite;
	}

	@keyframes gradient {
		0% {
			background-position: 0% 50%;
		}
		50% {
			background-position: 100% 50%;
		}
		100% {
			background-position: 0% 50%;
		}
	}`;

	const copyFunction = () => {
		// Copy the text inside the text field
		navigator.clipboard.writeText(css);

		// Alert the copied text
		alert('Copied the styles');
	};

	// function for the Random button

	const changeGradient = () => {
		isRandom = !isRandom;

		// Generate random colors
		clrList = [getRandomColor(), getRandomColor(), getRandomColor(), getRandomColor()];
	};

	function getRandomColor() {
		const letters = '0123456789ABCDEF';
		let color = '#';
		for (let i = 0; i < 6; i++) {
			color += letters[Math.floor(Math.random() * 16)];
		}
		return color;
	}

	// A flag to toggle between linear and radial gradients
	let gradientType: 'linear' | 'angular' | 'radial' = 'linear';

	const toggleGradientType = () => {
		if (gradientType === 'linear') {
			gradientType = 'angular';
		} else if (gradientType === 'angular') {
			gradientType = 'radial';
		} else {
			gradientType = 'linear';
		}
	};

	$: bgGradient =
		gradientType === 'linear'
			? clrList.length > 1
				? `background-image: linear-gradient(${angle}deg, ${clrList});`
				: `background-color: ${clrList};`
			: gradientType === 'angular'
			? clrList.length > 1
				? `background-image: conic-gradient(from ${angle}deg, ${clrList});`
				: `background-color: ${clrList};`
			: `background-image: radial-gradient(circle, ${clrList});`;
</script>

<Intro heading={data.meta.title} description={data.meta.description} />

<section class="bg-white dark:bg-gray-900">
	<br />
	<hr />

	<div
		class="color-div py-8 px-4 mx-auto max-w-screen-xl sm:py-16 lg:px-12 items-center content-center"
	>
		<!-- the color div part -->
		<div class="grid sm:grid-cols-6 md:grid-cols-8 lg:grid-cols-11 justify-items-center">
			<!-- <div class="w-2 m-2 p-6 bg-purple-800 border border-gray-200" /> -->
			{#each clrStyle as clr, i}
				<div class="relative">
					<button
						class="absolute top-[-4px] right-2 rounded-full h-6 w-6 bg-[#B8DBD9] flex justify-center items-center"
						on:click={removeOnClick}
						bind:this={myBtn}
						id={`${clrList[i]}`}
					>
						<svg
							class="cross w-[12px] h-[12px] text-gray-500"
							aria-hidden="true"
							xmlns="http://www.w3.org/2000/svg"
							fill="none"
							viewBox="0 0 14 14"
						>
							<path
								stroke="currentColor"
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="m1 1 6 6m0 0 6 6M7 7l6-6M7 7l-6 6"
							/>
						</svg>
					</button>
					<div class="aspect-square h-[50px] mx-4" style={clr} />
				</div>
			{/each}
		</div>

		<br />

		<!-- Color Palette part -->
		<div
			class="card gap-16 m-4 items-center mx-auto max-w-screen-xl md:grid md:grid-cols-2 overflow-hidden rounded-lg"
		>
			<div class="p-8 text-center">
				<Label class="mt-3 text-2xl">COLOR PALETTE</Label>
				<br />
				<Label class="mt-3">Choose color from the box</Label>
				<br />
				<div class="color-picker">
					<input type="color" id="color-input" bind:value={clrVal3} on:input={updateColorDisplay} />
					<!-- <input
                                type="range"
                                id="alpha-slider"
                                min="0"
                                max="1"
                                step="0.01"
                                value="1"
                                on:input={updateColorDisplay}
                            /> -->
					<div id="color-display" />
				</div>

				<div class="my-8">
					<button class="m-4 w-40 p-4 rounded-lg" on:click={pushArr}><b>+</b> Add color </button>
					<br />
					<button class="m-4 w-40 p-4 rounded-lg" on:click={changeGradient}> Random Colors </button>
					<br />
					<button class="m-4 w-40 p-4 rounded-lg" on:click={toggleGradientType}>
						{#if gradientType === 'linear'}
							Use Angular Gradient
						{:else if gradientType === 'angular'}
							Use Radial Gradient
						{:else}
							Use Linear Gradient
						{/if}
					</button>
				</div>

				<Label class="mt-3">ANGLE&nbsp;&nbsp;&nbsp;{angle}°</Label>
				<Range
					bind:value={angle}
					min="0"
					max="360"
					on:change={() => {
						console.log(angle);
					}}
				/>
				<br />
				<Label class="mt-3">DURATION&nbsp;&nbsp;&nbsp;{speed}s</Label>
				<Range
					bind:value={speed}
					min="1"
					max="20"
					on:change={() => {
						console.log(speed);
					}}
				/>
			</div>

			<div class="p-8 h-full flex rounded-lg relative output" style={bgGradient + gradientSpeed} />
		</div>
		<br />

		<!-- the text area part -->
		<div
			class="card m-4 p-1 bg-gray-100 items-center mx-auto max-w-screen-xl lg:grid rounded-lg relative"
		>
			<pre>{@html css}</pre>
			<Copy text={css} on:click={copyFunction} />
		</div>
	</div>
</section>

<style>
	/* Add a hover effect to your buttons */
	button {
		background-color: #b8dbd9;
		color: #2f4550;
		transition: background-color 0.3s ease;
		cursor: pointer;
	}

	button::after {
		background: rgba(0, 0, 0, 0);
		color: #b8dbd9;
		transition: all 0.3s ease;
	}

	button:hover {
		color: #b8dbd9;
		background-color: black;
	}

	button:hover::after {
		background: rgba(184, 219, 217, 0.3);
	}

	.card {
		box-shadow: rgba(0, 0, 0, 0.1) 0 0 0 2px;
	}

	:is(.dark .card) {
		box-shadow: rgba(255, 255, 255, 0.5) 0 0 0 2px;
	}

	.h-full {
		min-height: 300px;
	}

	.output {
		background-size: 400% 400%;
		animation: gradient 15s ease infinite;
	}

	@keyframes gradient {
		0% {
			background-position: 0% 50%;
		}
		50% {
			background-position: 100% 50%;
		}
		100% {
			background-position: 0% 50%;
		}
	}

	@media only screen and (max-width: 900px) {
		div {
			font-size: 2vw;
		}
	}

	.cross {
		pointer-events: none;
	}
</style>
