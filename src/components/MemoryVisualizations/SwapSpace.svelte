<script >
    import MemAddrRow from './MemAddrRow.svelte';
    
    export let processes;
    export let changePBit;
    export let changeVBit;
    export let curRunningProcessID;
    export let pagesNeededVPNs
    export let state;

    const swap_count = [{id: 0},{id:1},{id:2},{id:3},{id:4},{id:5},{id:6},{id:7},{id:8},{id:9}];
    const flipDurationMs = 300;
    const inSwapSpace = true;

</script>

<div class="flex flex-col gap-5">
    <h1 class="text-center">
        Swap Space (Disk) 
    </h1>

    <table class="table-xs table-auto w-60
    table-pin-rows table-pin-cols bg-base-200 
    border-separate border-spacing-y-1 ">
        <!-- head -->
        <thead>
            <tr>
                <!-- rounded to make sure edges of table are not cut -->
                <th class="bg-base-200  rounded-2xl text-xs">Block #</th>
                <th class="bg-base-200  rounded-2xl text-xs">PID</th>
                <th class="bg-base-200  rounded-2xl text-xs">VPN</th>
                <th class="bg-base-200  rounded-2xl text-xs">Frequency</th>
            </tr>
        </thead>
        {#each swap_count as swap_area, PFN (swap_area.id)}
            <MemAddrRow {PFN} {changePBit} {changeVBit} {inSwapSpace} {processes} {curRunningProcessID} bind:pagesNeededVPNs={pagesNeededVPNs} {state} />
        {/each}
    </table>
</div>