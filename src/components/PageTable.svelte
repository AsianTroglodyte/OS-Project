<script >
    import { TRIGGERS, dndzone, SHADOW_ITEM_MARKER_PROPERTY_NAME} from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';
    import { fade } from 'svelte/transition';
	
	export let pageElems = [{id: 0, PID: 0, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}];
    export let printPageElems;

	/**
     * @param {{ detail: { items: { id: number; letter: string; }[]; }; }} e
     */


    // I don't really know how this works https://svelte.dev/repl/924b4cc920524065a637fa910fe10193?version=3.59.2
    let shouldIgnoreDndEvents = false;
    function handleDndConsider(e) {

        const {trigger, id} = e.detail.info;
        if (trigger === TRIGGERS.DRAG_STARTED ) {
            const idx = pageElems.findIndex(pageElem => pageElem.id === id);
            // the following ensures a unique id 
            // try to ensure that newId is not duplicate
            let newId = Math.round(Math.random()*100000);
            // the line below was added in order to be compatible with version svelte-dnd-action 0.7.4 and above 
            e.detail.items = e.detail.items.filter(item => !item[SHADOW_ITEM_MARKER_PROPERTY_NAME]);
            e.detail.items.splice(idx, 0, {...pageElems[idx], id: newId});
            pageElems = e.detail.items;
            // console.log(pageElems[pageElems.findIndex(pageElem => pageElem.id === id)].PresentBit);
            shouldIgnoreDndEvents = true;
        }
        else if (!shouldIgnoreDndEvents) {
            pageElems = e.detail.items;
        }
        else {
            pageElems = [...pageElems];
        }


    }

    function handleDndFinalize(e) {
        // note that every combination of ID and PID is unique 
        if (!shouldIgnoreDndEvents) {
            // e.detail.items.PresentBit === 0;
            pageElems = e.detail.items;
        }
        else {
            pageElems = [...pageElems];
            shouldIgnoreDndEvents = false;
            console.log("finalized");
            printPageElems();
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

	const flipDurationMs = 100;
	
    $: options = {
        items: pageElems,
        flipDurationMs,
        transformDraggedElement,
        morphDisabled: true,
    };
</script>

<style>
</style>

<div class="flex flex-col gap-5 ">
    <div class="text-center">
        Page Table 
    </div>
    
    <div class="overflow-y-auto w-60" style="height:74vh" >
        <table class="table-xs table-auto w-full 
        table-pin-rows table-pin-cols bg-base-200 
        border-separate border-spacing-y-1 ">

            <thead>
                <tr>
                    <th class="text-xs">VPN</th>
                    <th class="text-xs">PID</th>
                    <th class="text-xs">V-Bit</th>
                    <th class="text-xs">P-Bit</th>
                </tr>
            </thead>

            <tbody  class="box-border rounded"
            use:dndzone={options} on:consider={handleDndConsider} on:finalize={handleDndFinalize}>
                {#each pageElems as pageElem, VPN (pageElem.id)}

                        <tr class = "bg-primary w-full h-10 text-white shadow-lg font-mono rounded">
                            <th class="text-center "> {VPN} </th>
                            <td class="text-center "> {pageElem.PID} </td>
                            <td class="text-center "> {pageElem.ValidBit} </td>
                            <td class="text-center "> {pageElem.PresentBit} </td>
                        </tr>
                {/each}
            </tbody>
        </table>
    </div>
</div>
