<script>
    import { dndzone } from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';
    import MemAddr from './MemAddr.svelte';
	
	
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

<div class="flex flex-col gap-5 ">
    <div class="text-center">
        Page Table
    </div>
    
    <div class="overflow-y-auto" style="height:74vh" >
        <div class="flex w-48 flex-wrap justify-center box-border
        bg-gradient-to-r bg-gradient-to-r from-sky-500 to-indigo-600 
        gap-2 p-2 overflow-y-auto rounded "
        use:dndzone={options} on:consider={handleDnd} on:finalize={handleDnd}>
            {#each items as item(item.id)}
                <div class= "bg-primary w-full h-12 text-white shadow-lg font-mono rounded">
                    {item.letter}
                </div>
            {/each}
        </div>
    </div>
</div>