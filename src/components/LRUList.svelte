<script>
    // the default value is very important it allows for implicit 
    // typing. code refuses "any" 
    export let processes = [{PID: 0, VPN: "00", PFN: "00"}];
</script>

<div class="flex flex-col gap-5 m-3">
    <h1 class = "text-center"> Least Recently Used</h1>

    <table class="table table-auto bg-base-200
    border-separate border border-slate-500 ">
        
        <!-- head -->
        <thead>
            <tr class = "w-24">
                <th>PID</th>
                <th>Job</th>
            </tr>
        </thead>
        <tbody>
            {#each processes as process}
                <tr>
                    <th class="bg-base-200  rounded-2xl">{process.PID}</th>
                    <th class="bg-base-200  rounded-2xl">{process.VPN}</th>
                    <th class="bg-base-200  rounded-2xl">{process.PFN}</th>
                </tr>
            {/each}
        </tbody>
    </table>
</div>