<script>
    // the default value is very important it allows for implicit 
    // typing. code refuses "any" 
    export let processes = [{PID: 0, VPN: "00", PFN: "00"}];
    
    let addProcess = () => {
        processes = [...processes, {PID: processes.length + 1, VPN: "bruh", PFN:"bruh"}];
        console.log(processes);
    }
</script>


<div class="flex flex-col ">
    <!-- Process Label -->
    <div class="flex flex-row gap-5 m-3">
        <h1 class="text-center content-center text-2xl">Processes List</h1>
        <button 
            class="btn btn-secondary p-4 btn-xs content-center"
            on:click={addProcess}>
            +
        </button>
    </div>

    <!-- Process list in the form of a table -->
    <table class="table table-auto 
        border-separate border border-slate-500 
        table-pin-rows table-pin-cols 
        bg-base-200">
        <!-- head -->
        <thead>
            <tr class="w-24">
                <!-- rounded to make sure edges of table are not cut -->
                <th class="bg-base-200  rounded-2xl">PID</th>
                <th class="bg-base-200"> VPN</th>
                <th class="bg-base-200  rounded-2xl">PFN</th>
            </tr>
        </thead>
        <!-- Body -->
        <tbody>
            {#each processes as process}
                <tr>
                    <!-- rounded to make sure edges of table are not cut -->
                    <th class="bg-base-200 rounded-2xl">{process.PID}</th>
                    <td>{process.VPN}</td>
                    <td class="flex flex-row border-collapse  border-slate-500 rounded-2xl" style="justify-content: space-between;">
                        <p class="content-center">{process.PFN}</p> 
                        <button class="btn btn-square btn-outline btn-sm ">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                        </button>
                    </td>
                </tr>
            {/each}
        </tbody>
    </table>
</div>