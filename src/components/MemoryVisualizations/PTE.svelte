<script>
// @ts-nocheck
    // Page Table Entry (PTE). this component is meant to be reused by Page Table component. It is the row along with all the DND logic.
    // I deemed it unique enough to warrant a seperate component from MemAddrRow.Svelte
    import { TRIGGERS, dndzone, SHADOW_ITEM_MARKER_PROPERTY_NAME} from 'svelte-dnd-action';


    export let pageElems;
    export let index;
    export let processes;
    export let pagesNeededVPNs;
    export let curRunningProcessID;
    function printStuff(stuff) {
        console.log("curRunningProcessID", stuff);
    }
    $: printStuff(curRunningProcessID);

    $: items = [pageElems[index]];

    // makes sure that the area we are draggin from has a copy in place while dragging
    let shouldIgnoreDndEvents = false;
    function handleDndConsider(e) {
        // console.log("PTE: ",e.detail.info.trigger)
        const {trigger, id} = e.detail.info;
        if (trigger === TRIGGERS.DRAG_STARTED ) {
            const idx = items.findIndex(item => item.id === id);
            // the following ensures a unique id 
            // try to ensure that newId is not duplicate
            let newId = Math.round(Math.random()*100000);
            // the line below was added in order to be compatible with version svelte-dnd-action 0.7.4 and above 
            e.detail.items = e.detail.items.filter(item => !item[SHADOW_ITEM_MARKER_PROPERTY_NAME]);
            e.detail.items.splice(idx, 0, {...items[0], id: newId});
            items = e.detail.items;
            shouldIgnoreDndEvents = true;
        }
        else if (!shouldIgnoreDndEvents) {
            items = e.detail.items;
        }
        else {
            items = [...items];
        }
    }


    // this function makes sure that when we successfully drop an item in another place
    // we know. The page is then rerendered automtically because allocated variable is part of reactive options below
    let allocated = false;
    function handleDndFinalize(e) {
        // note that every combination of ID and PID is unique 
        if (!shouldIgnoreDndEvents) {
            // e.detail.items.PresentBit === 0;
            items = e.detail.items;
            allocated = true;
        }
        else {
            items = [...items];
            shouldIgnoreDndEvents = false;
        }
        // disabling drags and drops after a successful drop into another place
        if (e.detail.info.trigger === "droppedIntoAnother") {
            allocated = true;
        }

        // checking if all required pages for a process are in PAS then running that process
    }
    
    // styling here is important because the contents of the tables are not formatted by their direct parents,
    // the <tr> element. Instead they are formatted by the <table>. This means that they become unformatted 
    // while being dragged as <tr> cannot use regular styling to make up for this without destroying the table format
    // NOTE: this option can only affect children of calling element 
    // the following really neads https://svelte.dev/repl/3d8be94b2bbd407c8a706d5054c8df6a?version=3.59.2

    // function transformDraggedElement(draggedEl, data, index) {
    //     if (draggedEl.querySelector('.text-center') !== null) {
    //         draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
    //                     bg-primary w-full h-12 text-white font-mono rounded"> </div>`;
    //     }
    //     // fix the following:
    //     // draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
    //     //                 bg-primary w-full h-12 text-white font-mono rounded">
    //     //                                 <p>VPN: ${data.VPN}</p>
    //     //                                 <p>PID: ${data.PID}</p>
    //     //                                 <p>V-Bit: ${data.ValidBit}</p>
    //     //                                 <p>P-Bit: ${data.PresentBit}</p>
    //     //                             </div>`;
	// }


    // takes the passed in processes and removes data if PID is not found in Process table
    function handleProcessDeletion(processes) {
        if ((items.length !== 0 && (processes.find((process) =>  process.id === items[0].PID) === undefined))) {
            items = [];
        }
    }
    $: handleProcessDeletion(processes);

    const flipDurationMs = 100;
    
    // options / how the dnd zone works must change depending on information
    // thus a reactive declaration
    $: options = {
        items: items,
        flipDurationMs,
        dropFromOthersDisabled: true,
        dragDisabled: allocated,
        // transformDraggedElement,
        morphDisabled: true,
    };

    // to make sure that items are draggable when the current running process is changed
    // don't ask why. drag gets disabled when a process is run and some pages are already allocated
    function handleCurRunningProcessID() {
        if (curRunningProcessID === items[0].PID && !allocated) {
            options.dragDisabled = false;
        }
    }
    $: curRunningProcessID, handleCurRunningProcessID();
</script>

<tbody  class="box-border rounded"
use:dndzone={options} on:consider={handleDndConsider} on:finalize={handleDndFinalize}>
    <!-- There is a subtlety here worth mentioning: the each block is to ensure that when a copy is made that it is actually displayed.
    if this doesn't make sense to you, play close attention to the implementation of the drag and drop copy logic: handleDndConsider()-->
    {#each items as item, index (item.id)}
        <!-- there is probably a much more efficeint way to do this... TOO BAD!!! -->
        {#if allocated === true}
            {#if (items.length !== 0) && (curRunningProcessID === items[0].PID) 
            && pagesNeededVPNs.filter(neededPage => neededPage.VPN === items[0].VPN).length !== 0}
                <tr class = "bg-teal-600 w-full h-9 text-white shadow-lg font-mono rounded">
                    <th class="text-center text-base"> {items[index].VPN} </th>
                    <td class="text-center text-base"> {items[index].PID} </td>
                    <td class="text-center text-base"> {items[index].ValidBit} </td>
                    <td class="text-center text-base"> {items[index].PresentBit} </td>
                </tr>
            {:else}
                <tr class = "bg-indigo-600 w-full h-9 text-white shadow-lg font-mono rounded">
                    <th class="text-center text-base"> {items[index].VPN} </th>
                    <td class="text-center text-base"> {items[index].PID} </td>
                    <td class="text-center text-base"> {items[index].ValidBit} </td>
                    <td class="text-center text-base"> {items[index].PresentBit} </td>
                </tr>
            {/if}
        {:else}
            {#if (items.length !== 0) && (curRunningProcessID === items[0].PID) 
            && pagesNeededVPNs.filter(neededPage => neededPage.VPN === items[0].VPN).length !== 0}
                <tr class = "bg-accent w-full h-9 text-white shadow-lg font-mono rounded">
                    <th class="text-center text-base"> {items[index].VPN} </th>
                    <td class="text-center text-base"> {items[index].PID} </td>
                    <td class="text-center text-base"> {items[index].ValidBit} </td>
                    <td class="text-center text-base"> {items[index].PresentBit} </td>
                </tr>
            {:else}
                <tr class = "bg-primary w-full h-9 text-white shadow-lg font-mono rounded">
                    <th class="text-center text-base"> {items[index].VPN} </th>
                    <td class="text-center text-base"> {items[index].PID} </td>
                    <td class="text-center text-base"> {items[index].ValidBit} </td>
                    <td class="text-center text-base"> {items[index].PresentBit} </td>
                </tr>
            {/if}
        {/if}
    {/each}
</tbody>