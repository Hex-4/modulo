<script>
	let { dotNum, nowDots } = $props();
</script>

{#if dotNum < nowDots}
	<div class="bg-text dot size-6 rounded-full"></div>
{:else if dotNum == nowDots}
	<div class="bg-primary dot size-6 rounded-full"></div>
{:else}
	<div class="bg-inactive dot size-6 rounded-full"></div>
{/if}
