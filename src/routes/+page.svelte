<script>
	import {
		differenceInSeconds,
		startOfDay,
		startOfHour,
		startOfMonth,
		startOfISOWeek,
		startOfYear,
		sub,
		isLeapYear
	} from 'date-fns';
	import Dot from '../components/dot.svelte';
	import { browser } from '$app/environment';
	import * as Lockr from 'lockr';
	import ColorPicker from 'svelte-awesome-color-picker';

	function changeBg() {
		document.querySelector(':root').style.setProperty('--bg-color', '#ffffff');
	}

	let unit = $state('day');
	let timerange = $state('month');

	let currentColors = $state(new Array(30).fill(''));
	let currentNotes = $state(new Array(30).fill(''));

	const secondsPer = {
		min: 60,
		hour: 60 * 60,
		day: 24 * 60 * 60,
		week: 7 * 24 * 60 * 60,
		month: 30 * 24 * 60 * 60, // average month
		year: 365 * 24 * 60 * 60 // non-leap year
	};

	let daysInMonth = $state([
		31,
		isLeapYear(new Date()) ? 29 : 28,
		31,
		30,
		31,
		30,
		31,
		31,
		30,
		31,
		30,
		31
	]);
	setInterval(
		() => {
			daysInMonth[1] = isLeapYear(new Date()) ? 29 : 28;
		},
		1000 * 60 * 60 * 24
	);

	let dots = $derived.by(() => {
		const secondsInUnit = secondsPer[unit];
		const secondsInRange = secondsPer[timerange];

		if (!secondsInUnit || !secondsInRange) {
			throw new Error('Invalid unit or timerange');
		}

		return Math.floor(secondsInRange / secondsInUnit);
	});
	let colsName = $state('month');
	let cols = $derived.by(() => {
		const secondsInUnit = secondsPer[unit];
		const secondsInRow = secondsPer[colsName];

		if (!secondsInRow || !secondsInUnit) {
			throw new Error('Invalid unit or timerange');
		}

		return Math.floor(secondsInRow / secondsInUnit);
	});

	// Amount of dots to highlight, the last one being now
	let nowDots = $state(0);
	setInterval(() => {
		let date = new Date();
		let start;
		if (timerange == 'hour') {
			start = startOfHour(date);
		} else if (timerange == 'day') {
			start = startOfDay(date);
		} else if (timerange == 'week') {
			start = startOfISOWeek(date);
		} else if (timerange == 'month') {
			start = sub(startOfMonth(date), { days: 1 });
		} else if (timerange == 'year') {
			start = sub(startOfYear(date), { days: 1 });
		}
		let diff = differenceInSeconds(date, start);
		nowDots = Math.floor(diff / secondsPer[unit]);
	}, 100);

	let dotElems = $derived.by(() => {
		if (browser) {
			return document.getElementsByClassName('dot');
		} else {
			return 0;
		}
	});

	function calcAmountOfDots(row, dot) {
		let dots = 0;
		for (const i of Array.from(Array(row).keys())) {
			dots += daysInMonth[i];
		}
		dots += dot + 1;
		return dots;
	}

	let rows = $derived(Math.ceil(dots / cols));
	let lastRow = $derived(dots % cols);

	let viewID = $derived(timerange + unit + colsName);

	function save() {
		console.log('SAVING!');
		Lockr.set(viewID + '_notes', currentNotes);
		Lockr.set(viewID + '_colors', currentColors);
	}

	function reset() {
		Lockr.rm(viewID + '_notes');
		Lockr.rm(viewID + '_colors');
		currentColors = new Array(30).fill('');
		currentNotes = new Array(30).fill('');
	}

	function getVar(varName) {
		if (browser) {
			return window.getComputedStyle(document.documentElement).getPropertyValue(varName);
		} else {
			return "#FFFFFF"
		}
	}

	$effect(() => {
		currentColors = Lockr.get(viewID + '_colors', new Array(30).fill(''));
		currentNotes = Lockr.get(viewID + '_notes', new Array(30).fill(''));
	});

	$inspect(currentColors);
</script>

{#snippet grid()}
	<div class="flex flex-col">
		{#each rows != Infinity ? { length: rows } : { length: 1 }, row}
			<div class="flex flex-row gap-1">
				{#each cols != null ? { length: cols } : { length: 1 }, dot}
					{#if lastRow == 0}
						<Dot
							dotNum={row * cols + dot + 1}
							{nowDots}
							bind:newColor={currentColors[calcAmountOfDots(row, dot) - 1]}
							bind:note={currentNotes[calcAmountOfDots(row, dot) - 1]}
						/>
					{:else if dot + 1 <= lastRow || row + 1 != rows}
						<Dot
							dotNum={row * cols + dot + 1}
							{nowDots}
							bind:newColor={currentColors[calcAmountOfDots(row, dot) - 1]}
							bind:note={currentNotes[calcAmountOfDots(row, dot) - 1]}
						/>
					{/if}
				{/each}
			</div>
		{/each}
	</div>
{/snippet}

<div class="bg-bg h-full min-h-screen w-full min-w-screen p-50 transition-all">
	{#if timerange === 'year' && colsName === 'month' && unit === 'day'}
		<div class="flex flex-col gap-1">
			{#each { length: 12 }, row}
				<div class="flex flex-row gap-1">
					{#each { length: daysInMonth[row] }, dot}
						<Dot
							dotNum={calcAmountOfDots(row, dot)}
							{nowDots}
							bind:newColor={currentColors[calcAmountOfDots(row, dot) - 1]}
							bind:note={currentNotes[calcAmountOfDots(row, dot) - 1]}
						/>
					{/each}
				</div>
			{/each}
		</div>
	{:else}
		{@render grid()}
	{/if}

	<br />

	<label for="time-range" class="text-text">showing</label>
	<select
		name="time-range"
		id="time-range"
		class="bg-inactive text-text hover:bg-primary hover:text-inactive rounded-lg p-1 transition-all duration-300"
		bind:value={timerange}
	>
		<option value="hour">this hour</option>
		<option value="day">this day</option>
		<option value="week">this week</option>
		<option value="month">this month</option>
		<option value="year">this year</option>
	</select>

	<label for="dot-is" class="text-text">with each dot being</label>
	<select
		name="dot-is"
		id="dot-is"
		class="bg-inactive text-text hover:bg-primary hover:text-inactive rounded-lg p-1 transition-all duration-300"
		bind:value={unit}
	>
		<option value="min">a minute</option>
		<option value="hour">an hour</option>
		<option value="day">a day</option>
		<option value="week">a week</option>
		<option value="month">a month</option>
		<option value="year">a year</option>
	</select>

	<label for="cols" class="text-text">and each row representing</label>
	<select
		name="cols"
		id="dot-is"
		class="bg-inactive text-text hover:bg-primary hover:text-inactive rounded-lg p-1 transition-all duration-300"
		bind:value={colsName}
	>
		<option value="hour">an hour</option>
		<option value="day">a day</option>
		<option value="week">a week</option>
		<option value="month">a month</option>
		<option value="year">a year</option>
	</select>

	<br />

	<button
		class="bg-inactive text-text hover:bg-primary hover:text-inactive mt-2 mr-2 rounded-lg px-3 py-1 transition-all duration-300"
		onclick={save}>save notes and colors to this view</button
	>
	<button
		class="bg-inactive text-text hover:bg-primary hover:text-inactive mt-2 mr-2 rounded-lg px-3 py-1 transition-all duration-300"
		onclick={reset}>reset saved data for this view</button
	>

	<br />

	<button
		class="bg-inactive text-text hover:bg-primary hover:text-inactive mt-2 mr-2 rounded-lg px-3 py-1 transition-all duration-300"
		popovertarget="theming-menu">open theming menu</button
	>

	<br>
	<br>

	<p class="text-text opacity-70 italic">click on a dot to change its color/note</p>

	<p class="text-text opacity-70 italic">Modulo is <a href="https://github.com/Hex-4/modulo" class="underline transition-all hover:underline-offset-5 underline-offset-2 hover:text-text">open source</a>, by <a class="underline transition-all hover:underline-offset-5 underline-offset-2 hover:text-text" href="https://hex4.pages.dev">hex4</a></p>

	<div
		popover
		class="bg-bg border-text absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex-row items-center gap-3 rounded-xl border-1 p-4 transition-all backdrop:backdrop-blur-sm open:flex overflow-visible"
		id="theming-menu"
	>
	<div class="picker flex flex-col gap-3 *:hover:font-extrabold *:transition-all *:duration-175">
		<ColorPicker hex={getVar("--bg-color")} label="Background" onInput={(event) => {document.querySelector(':root').style.setProperty('--bg-color', event.hex)}}/>
		<ColorPicker hex={getVar("--inactive-color")} label="Inactive" onInput={(event) => {document.querySelector(':root').style.setProperty('--inactive-color', event.hex)}}/>
		<ColorPicker hex={getVar("--active-color")} label="Active" onInput={(event) => {document.querySelector(':root').style.setProperty('--active-color', event.hex)}}/>
		<ColorPicker hex={getVar("--red-color")} label="Red" onInput={(event) => {document.querySelector(':root').style.setProperty('--red-color', event.hex)}}/>
		<ColorPicker hex={getVar("--yellow-color")} label="Yellow" onInput={(event) => {document.querySelector(':root').style.setProperty('--yellow-color', event.hex)}}/>
		<ColorPicker hex={getVar("--green-color")} label="Green" onInput={(event) => {document.querySelector(':root').style.setProperty('--green-color', event.hex)}}/>
		<ColorPicker hex={getVar("--blue-color")} label="Blue" onInput={(event) => {document.querySelector(':root').style.setProperty('--blue-color', event.hex)}}/>

	</div>
	
</div>
</div>

<style>
	.picker {
		--cp-bg-color: var(--bg-color);
		--cp-border-color: var(--text-color);
		--cp-text-color: var(--text-color);
		--cp-input-color: var(--inactive-color);
		--cp-button-hover-color: var(--active-color);
		--slider-width: 20px;
		--picker-indicator-size: 15px;
		--focus-color: var(--active-color);
		--picker-z-index: 100
	}
</style>
