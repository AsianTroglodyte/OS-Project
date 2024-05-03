<script>
    // @ts-nocheck
    // this component is meant to be reused by PAS and SwapSpace. It is the row along with all the DND logic
	import { dndzone } from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';


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
        // console.log("MemAddrRow: ",e.detail.items[0]);
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
        if (draggedEl.querySelector('.text-center') !== null && data.ValidBit === 1) {
            draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
                        bg-primary w-full h-12 text-white font-mono rounded">
                                        <p>PFN: ${data.PFN}</p>
                                        <p>PID: ${data.PID}</p>
                                        <p>V-Bit: ${data.VPN}</p>
                                    </div>`;
        }
        else if (draggedEl.querySelector('.text-center') !== null && data.ValidBit === 0) {
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

<!-- since <tbody> is effectively replacing the rows and the table containa border-spacing-y-1 class of the table, we have a table that grows by about 
2.5 REM everytime a row (or a table if we go by html). Therefore, a row is needed at all times to ensure consistent row-->
<tbody class="bg-primary w-full h-10 text-white font-mono rounded "
    use:dndzone={options} on:consider={handleConsider} on:finalize={handleFinalize}>

	{#if items.length === 1}
        {#each items as item (item.id)}
            <tr class= "bg-primary w-full h-10 text-white font-mono rounded "
            animate:flip={{ duration: flipDurationMs }}>
                <th calss="text-center  text-base">{PFN}</th>
                <td class="text-center  text-base">{item.PID}</td>
                <td class="text-center  text-base">{item.VPN}</td>
            </tr>
        {/each}
    {:else}
        <tr class="bg-base-200 w-full h-10 text-white shadow-lg font-mono rounded ">
            <th class="shadow-lg  text-base">{PFN}</th>
        </tr>
    {/if}
</tbody>