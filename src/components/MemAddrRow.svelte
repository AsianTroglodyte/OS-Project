<script>
// @ts-nocheck

    import { TRIGGERS, dndzone, SHADOW_ITEM_MARKER_PROPERTY_NAME} from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';
    import SwapSpace from './SwapSpace.svelte';


	export let PFN;
    export let processes;
    export let changePBit;
    export let changeVBit;
    export let inSwapSpace;

    let items = [];

    function handleMessage(event) {
		alert(event.detail.text);
	}

    // the following handle throws error because sometimes when ran items does not contain anything
    // therefore we check first.
	function handleConsider(e) {
		items = e.detail.items;
    }

    function handleFinalize(e) {
        items = e.detail.items;
        if (items.length === 1) {
            changePBit(items[0].PID, items[0].VPN, 1)
            if (inSwapSpace) {
                changeVBit(items[0].PID, items[0].VPN, 0)
                items[0].ValidBit = 0;
            }
            else {
                changeVBit(items[0].PID, items[0].VPN, 1)
                items[0].ValidBit = 1;
            }
        }
    }
	
    // styling element when being dragged based on 
    function transformDraggedElement(draggedEl, data, index) {
        // recall: PresentBit = 1 means in swap space and PresentBit = 0 means in Physical Address Space
        if (draggedEl.querySelector('.text-center') !== null && data.PresentBit === 1) {
            draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
                        bg-primary w-full h-12 text-white font-mono rounded">
                                        <p>PFN: ${data.PFN}</p>
                                        <p>PID: ${data.PID}</p>
                                        <p>V-Bit: ${data.VPN}</p>
                                    </div>`;
        }
        else if (draggedEl.querySelector('.text-center') !== null && data.PresentBit === 0) {
                        draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
                        bg-primary w-full h-12 text-white font-mono rounded">
                                        <p>Block #: ${data.BlockID}</p>
                                        <p>PID: ${data.PID}</p>
                                        <p>V-Bit: ${data.VPN}</p>
                                    </div>`;
        }
	}


    // delete the contents of items if the process cannot be found in the process table (in the process table's array at least)
    // I know its terrible because it updates on every process table change. instead of every deletion.
    // Also the bang at the beginning of the conditional is very wonky, but I'm strapped for time rn.
    function handleProcessDeletion(processes) {
        if (!(items.length === 1 && processes.find((process) =>  process.id === items[0].PID) !== undefined)) {
            items = [];
        }
    }
    $: handleProcessDeletion(processes);

	const flipDurationMs = 100;
	
    // options / how the dnd zone works must change depending on information
    // thus a reactive declaration
	$: options = {
		dropFromOthersDisabled: items.length,
		items: items,
		dropTargetStyle: {},
        transformDraggedElement,
		flipDurationMs: 0,
        dragDisabled: items.length === 1?  false: true,
	};
</script>

<!-- the situation here is strange indeed. since <tbody> is effectively replacing the rows
this means that we have a table that grows by about 2.5 REM everytime the list is updated because 
of the border-spacing-y-1 class of the table. 
Therefore, a row is needed at all times to ensure consistent spacing.
row-->
<tbody class="bg-primary w-full h-10 text-white font-mono rounded "
    use:dndzone={options} on:consider={handleConsider} on:finalize={handleFinalize}>

	{#if items.length === 1}
        {#each items as item (item.id)}
            <tr class= "bg-primary w-full h-10 text-white font-mono rounded "
            animate:flip={{ duration: flipDurationMs }}>
                <th>{PFN}</th>
                <td class="text-center">{item.PID}</td>
                <td class="text-center">{item.VPN}</td>
            </tr>
        {/each}
    {:else}
        <tr class="bg-base-200 w-full h-10 text-white shadow-lg font-mono rounded ">
            <th class="shadow-lg">{PFN}</th>
        </tr>
    {/if}
</tbody>