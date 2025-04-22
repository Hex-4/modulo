<script>
	let { dotNum, nowDots } = $props();

	import ColorPicker, { ChromeVariant } from 'svelte-awesome-color-picker';

	let popover = $state();

	function openPopover() {
		console.log(popover);
		popover.showPopover();
	}

	let color = $state()

	if (dotNum < nowDots) {
		color = "bg-text"
	} else if (dotNum == nowDots) {
		color = "bg-primary"
	} else {
		color = "bg-inactive"
	}

</script>

{#snippet picker()}
	<div popover class="bg-bg border-text rounded-xl border-1 p-4" bind:this={popover}>
		<div class="flex flex-row gap-1">
			<div class="w-3 h-6 rounded-full hover:w-6 transition-all bg-red" onclick={() => { color = "bg-red"}}></div>
			<div class="w-3 h-6 rounded-full hover:w-6 transition-all bg-yellow" onclick={() => { color = "bg-yellow"}}></div>
			<div class="w-3 h-6 rounded-full hover:w-6 transition-all bg-green" onclick={() => { color = "bg-green"}}></div>
			<div class="w-3 h-6 rounded-full hover:w-6 transition-all bg-blue" onclick={() => { color = "bg-blue"}}></div>
			<div class="w-3 h-6 rounded-full hover:w-6 transition-all bg-primary" onclick={() => { color = "bg-primary"}}></div>
			<div class="w-3 h-6 rounded-full hover:w-6 transition-all bg-inactive" onclick={() => { color = "bg-inactive"}}></div>
		</div>
	</div>
{/snippet}

	<div id = "test">
		<button onclick={openPopover} aria-label="Open Dot Dialog">
			<div class={["dot size-6 rounded-full", color]}></div>
			{@render picker()}
		</button>
		{@render picker()}
	</div>
