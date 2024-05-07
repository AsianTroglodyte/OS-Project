<script >
    import PTE from './PTE.svelte';
	
	export let pageElems;
    export let processes;
    export let pagesNeededVPNs;
    export let curRunningProcessID;
</script>

<style>
</style>

<div class="flex flex-col gap-5 ">
    <div class="text-center">
        Page Table 
    </div>
    
    <div class="overflow-y-auto w-60" style="height:67vh" >
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

            {#if pageElems !== undefined}
                {#each pageElems as pageElem, index (pageElem.id)}
                    <PTE {pageElems}{index} {processes} {pagesNeededVPNs} {curRunningProcessID}/>
                {/each}
            {/if}
        </table>
    </div>
</div>