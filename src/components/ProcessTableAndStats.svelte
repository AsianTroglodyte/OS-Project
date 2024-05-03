<script lang="ts">
    import { fade } from "svelte/transition";
    // need to add explicit typing 
    export let processes;
    export let addProcess = (ProcessType: string) => {};
    export let removeProcess = (id: number) => {};

    // for table border: border-separate border border-slate-500 
</script>



<div class="flex flex-col align-items gap-5">
    <!-- Process Label -->
    <h1 class="text-center">Process Table</h1>
    <div class="flex flex-row gap-5 justify-center">
        <button class="btn btn-xs btn-primary bg-primary" on:click={() => addProcess("P1")}>
            Add P1
        </button>
        <button class="btn btn-xs btn-primary  bg-primary" on:click={() => addProcess("P2")}>
            Add P2
        </button>
        <button class="btn btn-xs btn-primary bg-primary" on:click={() => addProcess("P3")}>
            Add P3
        </button>
    </div>
    

    <!-- Process list in the form of a table -->
    <!-- border-separate border-2 border-slate-500 rounded-lg -->
    <div class="overflow-y-auto max-h-80 h-80 w-60 bg-base-200 ">
        <table class="table-xs table-auto w-full
        table-pin-rows table-pin-cols bg-base-200 ">

            <!-- head -->
            <thead>
                <tr>
                    <!-- rounded to make sure edges of table are not cut -->
                    <th class="bg-base-200  rounded-2xl text-xs">PID</th>
                    <th class="bg-base-200  rounded-2xl text-xs">Process Type</th>
                    <th> </th>
                </tr>
            </thead>

            <!-- Body -->
            <tbody >
                {#if processes !== undefined}
                    {#each processes as process}
                        <tr transition:fade>
                            <!-- rounded to make sure edges of table are not cut -->
                            <th class="bg-base-200 rounded-2xl">{process.id}</th>
                            <td class="border-collapse border-slate-500 rounded-2xl" >
                                <p class="text-center">{process.processType} </p> 
                            </td>
                            <td class="flex flex-row border-collapse border-slate-500 rounded-2xl gap-1">
                                <button class="btn btn-square btn-outline btn-sm"
                                    on:click={() => removeProcess(process.id)}>
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                                </button>
                                <button class="btn btn-square btn-outline btn-sm">
                                    Run
                                </button>
                            </td>
                        </tr>
                    {/each}
                {/if}
            </tbody>
        </table>
    </div>
</div>


<div class="flex flex-col gap-5 max-h-80">
    <h1 class = "text-center"> Least Frequently Used Pages</h1>
    <div class="overflow-y-auto max-h-80 h-48 bg-base-200 ">
        <table class="table-xs table-auto bg-base-200 w-full">
            <!-- head -->
            <thead>
                <tr>
                    <th class="text-xs">PID</th>
                    <th class="text-xs">VPN</th>
                    <th class="text-xs">Frequency</th>
                </tr>
            </thead>
            <tbody>
                {#if processes !== undefined}
                    {#each processes as process}
                        <tr>
                            <th class="bg-base-200  rounded-2xl text-center">{process.id}</th>
                            <td class="bg-base-200  rounded-2xl text-center">{process.processType}</td>
                        </tr>
                    {/each}
                {/if}
            </tbody>
        </table>
    </div>
</div>

<div class="flex flex-col gap-5 max-h-80">
    <h1 class = "text-center"> Least Recently Used Pages</h1>
    <div class="overflow-y-auto max-h-80 h-48 bg-base-200 ">
        <table class="table-xs table-auto bg-base-200 w-full">
            
            <!-- head -->
            <thead>
                <tr>
                    <th class="text-xs">PID</th>
                    <th class="text-xs">VPN</th>
                    <th class="text-xs">Recency</th>
                </tr>
            </thead>
            <tbody>
                {#if processes !== undefined}
                    {#each processes as process}
                        <tr>
                            <th class="bg-base-200  rounded-2xl text-center">{process.id}</th>
                            <td class="bg-base-200  rounded-2xl text-center">{process.processType}</td>
                        </tr>
                    {/each}
                {/if}
            </tbody>
        </table>
    </div>
</div>