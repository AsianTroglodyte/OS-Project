<script>
    import { dndzone } from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';
    import { fade } from 'svelte/transition';
	
	
	export let pageElems = [{id: 0, PID: 0, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
	export let processes = [
        {id: 0, processType: "P1"},
    ];

    let currentlyDragged = false;
    let currentlyDraggedID = 0;
	
	/**
     * @param {{ detail: { items: { id: number; letter: string; }[]; }; }} e
     */
    // the following really neads https://svelte.dev/repl/3d8be94b2bbd407c8a706d5054c8df6a?version=3.59.2
	function handleCurrentlyDragged(e) {
		pageElems = e.detail.items;
        let currentlyDragged = true;
        currentlyDraggedID = e.detail.info.id;
        console.log(currentlyDragged)
        console.log(currentlyDraggedID)
        console.log(pageElems);
	}

    function handleFinishedDraggin(e) {
		pageElems = e.detail.items;
        let currentlyDragged = false;
	}
	
	const flipDurationMs = 100;
	
    $: options = {
        items: pageElems,
        flipDurationMs,
        morphDisabled: true
    };
</script>

<style>
    .border-solid.border-2.neutral.flex-row.justify-evenly.bg-primary.w-full.h-12.text-white.shadow-lg.font-mono.rounded.currentlyDragged {
        display:flex;
        background-color: red;
    }
</style>

<div class="flex flex-col gap-6 ">
    <div class="text-center">
        Page Table
    </div>
    
    <div class="overflow-y-auto w-60" style="height:74vh" >
        <!-- <div class="flex w-60 flex-row justify-evenly gap-2 p-2">
            <p>VPN</p>
            <p>PID</p>
            <p>V-Bit</p>
            <p>P-Bit</p>
        </div>
        
        <div class="flex w-60 flex-wrap justify-center box-border
        bg-gradient-to-r bg-gradient-to-r from-sky-500 to-indigo-600 
        gap-2 p-2 overflow-y-auto rounded"
        use:dndzone={options} on:consider={handleDnd} on:finalize={handleDnd}>
            {#each pageElems as pageElem , VPN (pageElem.id)}
                    <div class= " flex flex-row justify-evenly gap-2 
                    bg-primary w-full h-12 text-white shadow-lg font-mono rounded"
                    animate:flip={{ duration: flipDurationMs }}>
                        <p>{VPN}</p>
                        <p>{pageElem.PID}</p>
                        <p>{pageElem.ValidBit}</p>
                        <p>{pageElem.PresentBit}</p>
                    </div>
            {/each}
        </div> -->

        <table class="table-xs table-auto w-full 
        table-pin-rows table-pin-cols bg-base-200 
        border-separate border-spacing-y-1 ">

            <!-- head -->
            <thead>
                <tr>
                    <!-- rounded to make sure edges of table are not cut -->
                    <th class="bg-base-200  rounded-2xl text-xs">VPN</th>
                    <th class="bg-base-200  rounded-2xl text-xs">PID</th>
                    <th class="bg-base-200  rounded-2xl text-xs">V-Bit</th>
                    <th class="bg-base-200  rounded-2xl text-xs">P-Bit</th>
                </tr>
            </thead>

            <!-- Body try adding animate:flip={{ duration: flipDurationMs }} -->
            <tbody  class="box-border rounded"
            use:dndzone={options} on:consider={handleCurrentlyDragged} on:finalize={handleFinishedDraggin}>
                {#each pageElems as pageElem, VPN (pageElem.id)}
                    {#if currentlyDraggedID == pageElem.id && currentlyDragged == true}
                    <tr class = " border-solid border-2 neutral 
                    flex-row justify-evenly
                    bg-secondary w-full h-12 text-white shadow-lg font-mono rounded">
                        <!-- rounded to make sure edges of table are not cut -->
                        <th class="text-center "> {VPN} </th>
                        <td class="text-center "> {pageElem.PID} </td>
                        <td class="text-center "> {pageElem.ValidBit} </td>
                        <td class="text-center "> {pageElem.PresentBit} </td>
                    </tr>
                    {:else}
                    <tr class = "border-solid border-2 neutral 
                    flex-row justify-evenly
                    bg-primary w-full h-12 text-white shadow-lg font-mono rounded">
                        <!-- rounded to make sure edges of table are not cut -->
                        <th class="text-center "> {VPN} </th>
                        <td class="text-center "> {pageElem.PID} </td>
                        <td class="text-center "> {pageElem.ValidBit} </td>
                        <td class="text-center "> {pageElem.PresentBit} </td>
                    </tr>
                    {/if}
                {/each}
            </tbody>
        </table>
        

    </div>
</div>