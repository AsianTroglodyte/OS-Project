<script >
    import MemAddrRow from './MemAddrRow.svelte';

    export let processes;
    export let changePBit;
    export let changeVBit;
    export let curRunningProcessID;
    export let pagesNeededVPNs;
    export let state;
    export let runProcess = () => {};

    
    let inSwapSpace = false;

    // there is probably a more efficient way to do this. Anyways it allows for initializing empty rows.
    const mem_count =  [{id: 0},{id:1},{id:2},{id:3},{id:4},{id:5},{id:6},{id:7},{id:8},{id:9}];

    
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
                <th class="bg-base-200  rounded-2xl text-xs">Frequency</th>
            </tr>
        </thead>
        <!-- PFN acts as an index as well -->
        {#each mem_count as mem_area, PFN (mem_area.id)}
            <MemAddrRow {PFN} 
                        {processes} 
                        {changePBit}
                        {changeVBit} 
                        {inSwapSpace} 
                        {curRunningProcessID} 
                        {state}
                        bind:pagesNeededVPNs={pagesNeededVPNs}
                        {runProcess}/>
        {/each}
    </table>
</div>

