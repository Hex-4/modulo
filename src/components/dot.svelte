<script>
	let { dotNum, nowDots } = $props();
	import { onMount } from 'svelte';

	import ColorPicker, { ChromeVariant } from 'svelte-awesome-color-picker';

	let popover = $state();

	function openPopover() {
		console.log(popover);
		popover.showPopover();
	}

	let color = $state();

	$effect(() => {
		if (dotNum < nowDots) {
			color = 'bg-text';
		} else if (dotNum == nowDots) {
			color = 'bg-primary';
		} else {
			color = 'bg-inactive';
		}
	});

	$inspect(color);
</script>

{#snippet picker()}
	<div popover class="bg-bg border-text rounded-xl border-1 p-4 absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 backdrop:backdrop-blur-sm backdrop:transition-all" bind:this={popover}>
		<div class="flex flex-row gap-1">
			<button aria-label="Red"
				class="bg-red h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-red';
				}}
			></button>
			<button aria-label="Yellow"
				class="bg-yellow h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-yellow';
				}}
			></button>
			<button aria-label="Green"
				class="bg-green h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-green';
				}}
			></button>
			<button aria-label="Blue"
				class="bg-blue h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-blue';
				}}
			></button>
			<button aria-label="Primary"
				class="bg-primary h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-primary';
				}}
			></button>
			<button aria-label="Inactive"
				class="bg-inactive h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-inactive';
				}}
			></button>
		</div>
	</div>
{/snippet}

<div>
	<button onclick={openPopover} aria-label="Open Dot Dialog">
		<div class={['dot size-6 rounded-full', color]}></div>
		{@render picker()}
	</button>
	{@render picker()}
</div>
