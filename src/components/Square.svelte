<script>
	import { dndzone, SHADOW_ITEM_MARKER_PROPERTY_NAME } from 'svelte-dnd-action';

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
	}
	
	$: options = {
		dropFromOthersDisabled: items.length,
		items,
		dropTargetStyle: {},
		flipDurationMs: 0
	};

</script>


<div class="bg-primary w-28 h-20 text-white shadow-lg font-mono rounded"
    use:dndzone={options} on:consider={handleDnd} on:finalize={handleDnd}>
	{#each items as tile(tile.id)}
		<div class="bg-primary w-28 h-20 text-white shadow-lg font-mono rounded">
			{PFN}
		</div>
	{/each}
</div>

<style>
</style>