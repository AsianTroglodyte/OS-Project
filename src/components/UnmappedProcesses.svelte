<script>
	import { dndzone } from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';
	
	
	let idx = 0;
	
	let items = [
		{ id: idx++, letter: 'A' },
		{ id: idx++, letter: 'B' },
		{ id: idx++, letter: 'C' },
		{ id: idx++, letter: 'D' },
		{ id: idx++, letter: 'E' },
		{ id: idx++, letter: 'F' },
		{ id: idx++, letter: 'G' }
	];
	
	/**
     * @param {{ detail: { items: { id: number; letter: string; }[]; }; }} e
     */
	function handleDnd(e) {
		items = e.detail.items;
	}
	
	// of type { id: number }[][];
    const boardGrid = Array.from({ length: 2 }, (_, i) =>
        Array.from({ length: 5}, (_, j) => ({ id: i * 15 + j }))
    );
	
	const flipDurationMs = 100;
	
$: options = {
	items,
	flipDurationMs,
	morphDisabled: true
};
</script>
<div class="flex flex-row justify-center flex-wrap">
	
	<div use:dndzone={options} on:consider={handleDnd} on:finalize={handleDnd}>
		{#each items as item(item.id)}
            <div class="bg-primary w-48  h-10 text-white shadow-lg font-mono rounded" animate:flip={{ duration: flipDurationMs }}>
                
            </div>
		{/each}
    </div>
	
</div>
