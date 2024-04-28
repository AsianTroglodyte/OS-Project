<script>
    import {dndzone} from 'svelte-dnd-action';
    import MemAddr from './MemAddr.svelte';
    import UnmappedProcesses from './UnmappedProcesses.svelte';

    export let phys_addr_space_contents = [{id: 0, name:"bruh"}];


    const mem_count = Array(10);
    const boardGrid = Array.from({ length: 2 }, (_, i) =>
        Array.from({ length: 5}, (_, j) => ({ id: i * 15 + j }))
    );


    const flipDurationMs = 300;
    $: options = {
        phys_addr_space_contents,
        flipDurationMs,
        morphDisabled: true
    };
    
</script>

<div class="flex flex-col gap-5 ">
    <h1 class = "text-center">
        Physical Address Space (RAM)
    </h1>

	<div class="flex flex-row justify-center gap-2 w-56 flex-wrap
    bg-gradient-to-r bg-gradient-to-r from-sky-500 to-indigo-600 gap-2 p-2 rounded">
		{#each mem_count as mem_area, PFN}
			<!-- <div class="flex flex-col gap-2"> -->
			<MemAddr PFN={PFN} AreaType={"PFN"}/>
		    <!-- </div> -->
		{/each}
	</div>

</div>
