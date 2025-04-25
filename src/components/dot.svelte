<script>
	let { dotNum, nowDots, newColor = $bindable(), note = $bindable() } = $props();
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';

	import ColorPicker, { ChromeVariant } from 'svelte-awesome-color-picker';

	let popover = $state();
	let origColor = $state();

	function openPopover() {
		console.log(popover);
		popover.showPopover();
	}

	let color = $derived.by(() => {
		if (newColor) {
			console.log('USING NEW');
			return newColor;
		} else {
			return origColor;
		}
	});

	$effect(() => {
		if (dotNum < nowDots) {
			origColor = 'bg-text';
		} else if (dotNum == nowDots) {
			origColor = 'bg-primary';
		} else {
			origColor = 'bg-inactive';
		}
	});

	function lerp(i) {
		if (browser) {
			const t = i / (document.querySelectorAll('.dot').length - 1); // normalize 0 -> 1
			const eased = t * t;
			return eased * 3000; // max delay in ms
		} else {
			return 1;
		}
	}

	$inspect(newColor);
</script>

{#snippet picker()}
	<div
		popover
		class="bg-bg border-text absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex-row items-center gap-3 rounded-xl border-1 p-4 transition-all backdrop:backdrop-blur-sm open:flex"
		bind:this={popover}
	>
		<!-- svelte-ignore a11y_click_events_have_key_events -->
		<!-- svelte-ignore a11y_no_static_element_interactions -->

		<div class="flex flex-row gap-1">
			<div
				aria-label="Red"
				class="bg-red h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					newColor = 'bg-red';
				}}
			></div>
			<div
				aria-label="Yellow"
				class="bg-yellow h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					newColor = 'bg-yellow';
				}}
			></div>
			<div
				aria-label="Green"
				class="bg-green h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					newColor = 'bg-green';
				}}
			></div>
			<div
				aria-label="Blue"
				class="bg-blue h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					newColor = 'bg-blue';
				}}
			></div>
			<div
				aria-label="Primary"
				class="bg-primary h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					newColor = 'bg-primary';
				}}
			></div>
			<div
				aria-label="Inactive"
				class="bg-inactive h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					newColor = 'bg-inactive';
				}}
			></div>
			<div
				aria-label="Reset"
				class="border-text text-text h-6 w-3 rounded-full border-2 border-dashed transition-all hover:w-6"
				onclick={() => {
					newColor = "";
				}}
			></div>
		</div>
		<div class="bg-text h-10 w-[1px]"></div>
		<input
			type="text"
			bind:value={note}
			placeholder="add a note..."
			class="bg-inactive text-text text-md h-8 w-70 rounded-lg"
		/>
	</div>
{/snippet}

<div>
	<button onclick={openPopover} aria-label="Open Dot Dialog">
		<div class={['dot size-6 rounded-full transition-all duration-1000', color]}></div>
		{@render picker()}
	</button>
	{@render picker()}
</div>
