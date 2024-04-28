<script>
    import { dndzone } from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';
	
	
	let idx = 0;
	export let pageElems = [{id: 0, PID: 0, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
	export let processes = [
        {id: 0, processType: "P1"},
    ];
	
	/**
     * @param {{ detail: { items: { id: number; letter: string; }[]; }; }} e
     */
	function handleDnd(e) {
		pageElems = e.detail.items;
        console.log(processes);
	}
	
	const flipDurationMs = 100;
	
    $: options = {
        items: pageElems,
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
            {#each pageElems as pageElem (pageElem.id)}
                    <div class= "bg-primary w-full h-12 text-white shadow-lg font-mono rounded"
                    animate:flip={{ duration: flipDurationMs }}>
                        {pageElem.id}
                    </div>
            {/each}
        </div>
    </div>
</div>