<script>
// @ts-nocheck

	import { dndzone } from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';
    import SwapSpace from './SwapSpace.svelte';


	export let PFN;
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
        // console.log(items);
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

	const flipDurationMs = 100;
	
	$: options = {
		dropFromOthersDisabled: items.length,
		items: items,
		dropTargetStyle: {},
        transformDraggedElement,
		flipDurationMs: 0
	};

</script>


<tbody class="bg-primary w-full h-10 text-white font-mono rounded "
    use:dndzone={options} on:consider={handleConsider} on:finalize={handleFinalize}>
    <!-- the situation here is strange indeed. since <tbody> is effectively replacing the rows
    this means that we have a table that grows by about 2.5 REM everytime the list is updated because 
    of the border-spacing-y-1 class of the table. 
    Therefore, a row is needed at all times to ensure consistent spacing.
    row-->
	{#if items.length !== 0}
        {#each items as item (item.id)}
            <tr class= "bg-primary w-full h-10 text-white font-mono rounded "
            animate:flip={{ duration: flipDurationMs }}>
                <th>{PFN}</th>
                <td class="text-center">{item.id}</td>
                <td class="text-center">{item.VPN}</td>
            </tr>
        {/each}
    {:else}
        <tr class="bg-base-200 w-full h-10 text-white shadow-lg font-mono rounded ">
            <th class="shadow-lg">{PFN}</th>
        </tr>
    {/if}
</tbody>