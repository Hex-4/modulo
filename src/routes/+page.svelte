<script>
	import { differenceInSeconds, startOfYear } from 'date-fns';

	function changeBg() {
		document.querySelector(':root').style.setProperty('--bg-color', '#ffffff');
	}

	let unit = $state('day');
	let timerange = $state('month');

	const secondsPer = {
		min: 60,
		hour: 60 * 60,
		day: 24 * 60 * 60,
		week: 7 * 24 * 60 * 60,
		month: 30 * 24 * 60 * 60, // average month
		year: 365 * 24 * 60 * 60 // non-leap year
	};

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
		let start = startOfYear(date);
		let diff = differenceInSeconds(date, start);
		nowDots = diff / secondsPer[unit];
	}, 100);

	let rows = $derived(Math.ceil(dots / cols));
	let lastRow = $derived(dots % cols);
	$inspect(nowDots);
</script>

<div class="bg-bg h-screen w-screen p-50">
	<div class="flex flex-col gap-1">
		{#each rows != Infinity ? { length: rows } : { length: 1 }, row}
			<div class="flex flex-row gap-1">
				{#each cols != null ? { length: cols } : { length: 1 }, dot}
					{#if lastRow == 0}
						<div class="bg-primary size-6 rounded-full"></div>
					{:else if dot + 1 <= lastRow || row + 1 != rows}
						<div class="bg-primary size-6 rounded-full"></div>
					{/if}
				{/each}
			</div>
		{/each}
	</div>

	<br />

	<label for="time-range" class="text-text">showing</label>
	<select name="time-range" id="time-range" class="bg-inactive text-text" bind:value={timerange}>
		<option value="hour">this hour</option>
		<option value="day">this day</option>
		<option value="week">this week</option>
		<option value="month">this month</option>
		<option value="year">this year</option>
	</select>

	<label for="dot-is" class="text-text">with each dot being</label>
	<select name="dot-is" id="dot-is" class="bg-inactive text-text" bind:value={unit}>
		<option value="min">a minute</option>
		<option value="hour">an hour</option>
		<option value="day">a day</option>
		<option value="week">a week</option>
		<option value="month">a month</option>
		<option value="year">a year</option>
	</select>

	<label for="cols" class="text-text">and each row representing</label>
	<select name="cols" id="dot-is" class="bg-inactive text-text" bind:value={colsName}>
		<option value="hour">an hour</option>
		<option value="day">a day</option>
		<option value="week">a week</option>
		<option value="month">a month</option>
		<option value="year">a year</option>
	</select>
</div>
