<script lang="ts">
    import {dndzone} from 'svelte-dnd-action';
    import MemAddrRow from './MemAddrRow.svelte';
	import { flip } from 'svelte/animate';

    export let processes;
    export let changePBit;
    export let changeVBit;
    export let curRunningProcessID;
    export let pagesNeededVPNs;
    
    let inSwapSpace = false;

    // there is probably a more efficient way to do this. Anyways it allows for initializing empty rows.
    const mem_count =  [{id: 0},{id:1},{id:2},{id:3},{id:4},{id:5},{id:6},{id:7},{id:8},{id:9}];


    /**
     * @param {{ detail: { items: { id: number; letter: string; }[]; }; }} e
     */
    // the following really neads https://svelte.dev/repl/3d8be94b2bbd407c8a706d5054c8df6a?version=3.59.2






    let items = [];

    // the following handle throws error because sometimes when ran items does not contain anything
    // therefore we check first.
    function handleConsider(e) {
        // console.log("MemAddrRow: ", e.detail.items[0]);
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
        console.log("MemAddrRow finalize:", "\nitems:", items, "");
    }

    // styling element when being dragged. This is a bit difficult to get working

    // function transformDraggedElement(draggedEl, data, index) {
    //     // recall: PresentBit = 1 means in swap space and PresentBit = 0 means in Physical Address Space
    //     if (draggedEl.querySelector('.text-center') !== null && data.ValidBit === 1) {
    //         draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
    //                     bg-primary w-full h-12 text-white font-mono rounded"></div>`;
    //     }
    //     else if (draggedEl.querySelector('.text-center') !== null && data.ValidBit === 0) {
    //                     draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
    //                     bg-primary w-full h-12 text-white font-mono rounded"></div>`;
    //     }
    //             // fix the following as it is problematic:
    //             // draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
    //             //         bg-primary w-full h-12 text-white font-mono rounded">
    //             //                         <p>Block #: ${data.BlockID}</p>
    //             //                         <p>PID: ${data.PID}</p>
    //             //                         <p>V-Bit: ${data.VPN}</p>
    //             //                     </div>`;
    // }


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

    function printItems(dropFromOthersDisabled, items, dragDisabled) {
        console.log("print items ", "PFN: ", dropFromOthersDisabled, items, dragDisabled);
    }

    // options / how the dnd zone works must change depending on information
    // thus a reactive declaration
    $: options = {
        dropFromOthersDisabled: items.length,
        items: items,
        dropTargetStyle: {},
        // transformDraggedElement,
        flipDurationMs: 0,
        dragDisabled: items.length === 1?  false: true,
    };
    $: printItems(options.dropFromOthersDisabled, options.items, options.dragDisabled);
    
</script>

<div class="flex flex-col gap-5 ">
    <h1 class = "text-center">
        Physical Address Space (RAM)
    </h1>

    <table class="table-xs table-auto w-60
    table-pin-rows table-pin-cols bg-base-200 
    border-separate border-spacing-y-1 ">
        <!-- head -->
        <thead>
            <tr>
                <!-- rounded to make sure edges of table are not cut -->
                <th class="bg-base-200  rounded-2xl text-xs">PFN</th>
                <th class="bg-base-200  rounded-2xl text-xs">PID</th>
                <th class="bg-base-200  rounded-2xl text-xs">VPN</th>
            </tr>
        </thead>
        <!-- PFN acts as an index as well -->
        {#each mem_count as mem_area, PFN (mem_area.id)}


            <!-- since <tbody> is effectively replacing the rows and the table containa border-spacing-y-1 class of the table, we have a table that grows by about 
            2.5 REM everytime a row (or a table if we go by html). Therefore, a row is needed at all times to ensure consistent row-->
            <!-- <tbody class="bg-primary w-full h-10 text-white font-mono rounded "
            use:dndzone={options} on:consider={handleConsider} on:finalize={handleFinalize}>
                {#if (items.length !== 0) && (curRunningProcessID === items[0].PID)}
                    {#each items as item (item.id)}
                        <tr class= "bg-accent w-full h-10 text-white font-mono rounded "
                        animate:flip={{ duration: flipDurationMs }}>
                            <th class="text-center  text-base">{PFN}</th>
                            <td class="text-center  text-base">{item.PID}</td>
                            <td class="text-center  text-base">{item.VPN}</td>
                        </tr>
                    {/each}
                {:else if (items.length !== 0) && (curRunningProcessID !== items[0].PID)}
                    {#each items as item (item.id)}
                        <tr class= "bg-primary w-full h-10 text-white font-mono rounded "
                        animate:flip={{ duration: flipDurationMs }}>
                            <th class="text-center  text-base">{PFN}</th>
                            <td class="text-center  text-base">{item.PID}</td>
                            <td class="text-center  text-base">{item.VPN}</td>
                        </tr>
                    {/each}
                {:else if (items.length === 0)}
                    <tr class="bg-base-200 w-full h-10 text-white shadow-lg font-mono rounded ">
                        <th class="shadow-lg  text-base">{PFN}</th>
                    </tr>
                {/if}
            </tbody> -->
            <MemAddrRow {PFN} {processes} {changePBit}  {changeVBit} {inSwapSpace} {curRunningProcessID} {pagesNeededVPNs}/>
        {/each}
    </table>
</div>

