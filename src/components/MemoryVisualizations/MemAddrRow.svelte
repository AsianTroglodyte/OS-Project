<script>
    // @ts-nocheck
    // this component is meant to be reused by PAS and SwapSpace. It is the row along with all the DND logic
	import { dndzone } from 'svelte-dnd-action';
	import { flip } from 'svelte/animate';

    // consolidating more of these variables into a single powerful variable would be good: To do later.
	export let PFN;
    export let processes;
    export let changePBit;
    export let changeVBit;
    export let inSwapSpace;
    export let curRunningProcessID;
    export let state;
    export let pagesNeededVPNs = [];
    export let runProcess = () => {};

    let items = [];


    // the following handle throws error because sometimes when ran items does not contain anything
    // therefore we check first.
	function handleConsider(e) {
        // console.log("MemAddrRow: ", e.detail.items[0]);
		items = e.detail.items;
    }

    function handleFinalize(e) {
        // changing bits on page table and on items as needed
        items = e.detail.items;
        if (items.length === 1) {
            changePBit(items[0].PID, items[0].VPN, 1);
            if (inSwapSpace) {
                changeVBit(items[0].PID, items[0].VPN, 0);
                items[0].ValidBit = 0;

                // changing whether inPAS of pagdsNeededVPNs
                if (curRunningProcessID === items[0].PID && pagesNeededVPNs.filter(neededPage => neededPage.VPN === items[0].VPN).length !== 0) {
                    pagesNeededVPNs[pagesNeededVPNs.findIndex(neededPage => neededPage.VPN === items[0].VPN)].inPAS = false;
                }
            }
            else {
                changeVBit(items[0].PID, items[0].VPN, 1);
                items[0].ValidBit = 1;

                // changing whether inPAS of pagdsNeededVPNs
                if (curRunningProcessID === items[0].PID && pagesNeededVPNs.filter(neededPage => neededPage.VPN === items[0].VPN).length !== 0) {
                    pagesNeededVPNs[pagesNeededVPNs.findIndex(neededPage => neededPage.VPN === items[0].VPN)].inPAS = true;
                }
            }
        }
        
        // NOTE 
        if (pagesNeededVPNs.length > 0 && pagesNeededVPNs.every(neededPage => {return neededPage.inPAS === true;})) {
            runProcess();
        }
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

	
    // options / how the dnd zone works must change depending on information, thus a reactive declaration
	$: options = {
		dropFromOthersDisabled: items.length,
		items: items,
		dropTargetStyle: {},
        // transformDraggedElement,
		flipDurationMs: 0,
        dragDisabled: items.length === 1?  false: true,
    };
    
    // to make sure that items are draggable when the current running process is changed. 
    // don't ask why. drag gets disabled when a process is run and some pages are already allocated
    function handleCurRunningProcessID() {
        // console.log("handleCurRunningProcessID");
        options.dragDisabled = false;
        if (items.length > 0  
        && curRunningProcessID === items[0].PID 
        && pagesNeededVPNs.filter(neededPage => neededPage.VPN === items[0].VPN).length !== 0 
        && !inSwapSpace) {
            pagesNeededVPNs[pagesNeededVPNs.findIndex(neededPage => neededPage.VPN === items[0].VPN)].inPAS = true;
            if (pagesNeededVPNs.length > 0 && pagesNeededVPNs.every(neededPage => {return neededPage.inPAS === true;})) {
                runProcess();
            }
        }

        // updating frequency

        // console.log("items: ", items);
    }
    $: curRunningProcessID, handleCurRunningProcessID();

    // delete the contents of items if the process cannot be found in the process table (in the process table's array at least)
    // I know its terrible because it updates on every process table change. instead of every deletion.
    function handleProcessDeletion() {
        // console.log("handleProcessDeletion");
        if ((items.length > 0) && (processes.find((process) => {return process.id === items[0].PID}) === undefined)) {
            items = [];
            // somehow the dnd zone gets messed up so we need to reset it when a process is deleted
            options = {
                dropFromOthersDisabled: items.length,
                items: items,
                dropTargetStyle: {},
                // transformDraggedElement,
                flipDurationMs: 0,
                dragDisabled: items.length === 1?  false: true,
            };
        }
        // when process is deleted when it is running then drag is turned on. Here it is turned off. 
        // should refactor at a later time
        options.dragDisabled = true;
    }
    $: processes, handleProcessDeletion();

    function handleFrequencyUpdate(){
        if ( items.length !== 0        
        && state === "running process"
        && curRunningProcessID === items[0].PID
        && (pagesNeededVPNs.find((neededPage) => {return neededPage.VPN === items[0].VPN}) !== undefined)) {
            options.dragDisabled = true;
            items[0].Frequency += 1;
        }
    }
    $: state, handleFrequencyUpdate();

	const flipDurationMs = 100;

</script>


<tbody class="bg-primary w-full h-9 text-white font-mono rounded "
use:dndzone={options} on:consider={handleConsider} on:finalize={handleFinalize}>
    {#if (items.length !== 0) 
    && (curRunningProcessID === items[0].PID) 
    && (pagesNeededVPNs.filter(neededPage => neededPage.VPN === items[0].VPN).length !== 0)}
            {#each items as item (item.id)}
                <tr class= "bg-accent w-full h-9 text-white font-mono rounded "
                animate:flip={{ duration: flipDurationMs }}>
                    <th class="text-center  text-base">{PFN}</th>
                    <td class="text-center  text-base">{item.PID}</td>
                    <td class="text-center  text-base">{item.VPN}</td>
                    <td class="text-center  text-base">{item.Frequency}</td>
                </tr>
            {/each}
    {:else if (items.length !== 0)}
            {#each items as item (item.id)}
                <tr class= "bg-primary w-full h-9 text-white font-mono rounded "
                animate:flip={{ duration: flipDurationMs }}>
                    <th class="text-center  text-base">{PFN}</th>
                    <td class="text-center  text-base">{item.PID}</td>
                    <td class="text-center  text-base">{item.VPN}</td>
                    <td class="text-center  text-base">{item.Frequency}</td>
                </tr>
            {/each}
    {:else if (items.length === 0)}
            <tr class="bg-base-200 w-full h-9 text-white shadow-lg font-mono rounded ">
                <th class="shadow-lg  text-base">{PFN}</th>
            </tr>
    {/if}
</tbody>
