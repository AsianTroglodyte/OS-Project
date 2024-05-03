<script lang="ts">
    import {dndzone} from 'svelte-dnd-action';
    import MemAddrRow from './MemAddrRow.svelte';

    export let processes;
    export let changePBit;
    export let changeVBit;
    
    let inSwapSpace = false;

    // there is probably a more efficient way to do this. Anyways it allows for initializing empty rows.
    const mem_count =  [{id: 0},{id:1},{id:2},{id:3},{id:4},{id:5},{id:6},{id:7},{id:8},{id:9}];


    /**
     * @param {{ detail: { items: { id: number; letter: string; }[]; }; }} e
     */
    // the following really neads https://svelte.dev/repl/3d8be94b2bbd407c8a706d5054c8df6a?version=3.59.2


    
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
            <MemAddrRow {PFN} {changePBit} {changeVBit} {inSwapSpace} {processes}></MemAddrRow>
        {/each}
    </table>
</div>
