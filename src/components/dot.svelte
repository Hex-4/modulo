<script>
	let { dotNum, nowDots } = $props();

	import ColorPicker, { ChromeVariant } from 'svelte-awesome-color-picker';

	let popover = $state();

	function openPopover() {
		console.log(popover);
		popover.showPopover();
	}
</script>

{#snippet picker()}
	<div popover class="bg-inactive border-text rounded-xl border-1 p-4" bind:this={popover}>
		<ColorPicker components={ChromeVariant} sliderDirection="horizontal" isDialog={false} />
	</div>
{/snippet}

{#if dotNum < nowDots}
	<button onclick={openPopover} aria-label="Open Dot Dialog">
		<div class="bg-text dot size-6 rounded-full"></div>
	</button>
	{@render picker()}
{:else if dotNum == nowDots}
	<button onclick={openPopover} aria-label="Open Dot Dialog">
		<div class="bg-primary dot size-6 rounded-full"></div>
	</button>
	{@render picker()}
{:else}
	<button onclick={openPopover} aria-label="Open Dot Dialog">
		<div class="bg-inactive dot size-6 rounded-full"></div>
	</button>
	{@render picker()}
{/if}
