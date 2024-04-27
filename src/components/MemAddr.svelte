<script>
	import { dndzone, SHADOW_ITEM_MARKER_PROPERTY_NAME } from 'svelte-dnd-action';

	// IMPORTANT SQUARES ARE MADE WITH THE SOLE PURPOSE OF PROVIDING
	// EMPTY SPACES FOR PROCESSES
	/**
     * @type {any}
     */
	export let PFN;
	/**
     * @type {any[]}
     */
	let items = [];
	
	/**
     * @param {{ detail: { items: any[]; }; }} e
     */
	function handleDnd(e) {
		items = e.detail.items;
		console.log(items);
	}
	
	$: options = {
		dropFromOthersDisabled: items.length,
		items,
		dropTargetStyle: {},
		flipDurationMs: 0
	};

</script>


<div class="bg-primary w-full h-12 text-white shadow-lg font-mono rounded"
    use:dndzone={options} on:consider={handleDnd} on:finalize={handleDnd}>
	{#each items as tile(tile.id)}
		<div class= "bg-primary w-full h-12 text-white shadow-lg font-mono rounded">
			PFN:{PFN}
			{items[0].letter}
		</div>
	{/each}
</div>