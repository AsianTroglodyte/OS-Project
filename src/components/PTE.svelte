<script>
// @ts-nocheck

    import { TRIGGERS, dndzone, SHADOW_ITEM_MARKER_PROPERTY_NAME} from 'svelte-dnd-action';


    export let pageElems = [{id: 0, PID: 0, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}];
    export let index;
    export let processes;

    $: items = [pageElems[index]];

    let shouldIgnoreDndEvents = false;
    function handleDndConsider(e) {
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
            // console.log(pageElems[pageElems.findIndex(pageElem => pageElem.id === id)].PresentBit);
            shouldIgnoreDndEvents = true;
        }
        else if (!shouldIgnoreDndEvents) {
            items = e.detail.items;
        }
        else {
            items = [...items];
        }
        console.log("consider", items[0], e.detail.items);
    }

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
    }
    
    // styling here is important because the contents of the tables are not formatted by their direct parents,
    // the <tr> element. Instead they are formatted by the <table>. This means that they become unformatted 
    // while being dragged as <tr> cannot use regular styling to make up for this without destroying the table format
    // NOTE: this option can only affect children of calling element 
    // the following really neads https://svelte.dev/repl/3d8be94b2bbd407c8a706d5054c8df6a?version=3.59.2
    function transformDraggedElement(draggedEl, data, index) {
        if (draggedEl.querySelector('.text-center') !== null) {
            draggedEl.innerHTML = `<div class="flex flex-row justify-around items-center
                        bg-primary w-full h-12 text-white font-mono rounded">
                                        <p>VPN: ${data.VPN}</p>
                                        <p>PID: ${data.PID}</p>
                                        <p>V-Bit: ${data.ValidBit}</p>
                                        <p>P-Bit: ${data.PresentBit}</p>
                                    </div>`;
        }
	}


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
        items: items,
        flipDurationMs,
        dropFromOthersDisabled: allocated,
        dragDisabled: allocated,
        transformDraggedElement,
        morphDisabled: true,
    };
</script>

<!-- the situation here is strange indeed. since <tbody> is effectively replacing the rows
this means that we have a table that grows by about 2.5 REM everytime the list is updated because 
of the border-spacing-y-1 class of the table. 
Therefore, a row is needed at all times to ensure consistent spacing.
row-->
<tbody  class="box-border rounded"
use:dndzone={options} on:consider={handleDndConsider} on:finalize={handleDndFinalize}>
    {#each items as item (item.id)}
        <tr class = "bg-primary w-full h-10 text-white shadow-lg font-mono rounded">
            <th class="text-center "> {items[0].VPN} </th>
            <td class="text-center "> {items[0].PID} </td>
            <td class="text-center "> {items[0].ValidBit} </td>
            <td class="text-center "> {items[0].PresentBit} </td>
        </tr>
    {/each}
</tbody>