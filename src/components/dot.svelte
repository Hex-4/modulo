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
	let note = $state("")

	$effect(() => {
		if (dotNum < nowDots) {
			color = 'bg-text';
		} else if (dotNum == nowDots) {
			color = 'bg-primary';
		} else {
			color = 'bg-inactive';
		}
	})

	$inspect(color);


</script>

{#snippet picker()}
	<div popover class="bg-bg border-text rounded-xl border-1 p-4 absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 backdrop:backdrop-blur-sm transition-all open:flex flex-row items-center gap-3" bind:this={popover}>
		<!-- svelte-ignore a11y_click_events_have_key_events -->
		<!-- svelte-ignore a11y_no_static_element_interactions -->
		
		<div class="flex flex-row gap-1">
			<div aria-label="Red"
				class="bg-red h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-red';
				}}
			></div>
			<div aria-label="Yellow"
				class="bg-yellow h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-yellow';
				}}
			></div>
			<div aria-label="Green"
				class="bg-green h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-green';
				}}
			></div>
			<div aria-label="Blue"
				class="bg-blue h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-blue';
				}}
			></div>
			<div aria-label="Primary"
				class="bg-primary h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-primary';
				}}
			></div>
			<div aria-label="Inactive"
				class="bg-inactive h-6 w-3 rounded-full transition-all hover:w-6"
				onclick={() => {
					color = 'bg-inactive';
				}}
			></div>
		</div>
		<div class="w-[1px] h-10 bg-text"></div>
		<input type="text" bind:value={note} placeholder="add a note..." class="bg-inactive rounded-lg text-text h-8 text-md w-70">
	</div>
{/snippet}

<div>
	<button onclick={openPopover} aria-label="Open Dot Dialog">
		<div class={['dot size-6 rounded-full', color]}></div>
		{@render picker()}
	</button>
	{@render picker()}
</div>
