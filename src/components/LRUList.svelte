<script>
    // the default value is very important it allows for implicit 
    // typing. code refuses "any" 
    export let processes = [{PID: 0, VPN: "00", PFN: "00"}];
    // for borders on table: border-separate border border-slate-500 
</script>

<div class="flex flex-col gap-5 max-h-80 h-80">
    <h1 class = "text-center"> Least Recently Used</h1>
    <div class="overflow-y-auto max-h-80 h-80 
    border-separate border-2 border-slate-500 rounded-lg">
        <table class="table-xs table-auto bg-base-200 w-full">
            
            <!-- head -->
            <thead>
                <tr>
                    <th class="text-xs">PID</th>
                    <th class="text-xs">VPN</th>
                    <th class="text-xs">PFN</th>
                </tr>
            </thead>
            <tbody>
                {#each processes as process}
                    <tr>
                        <th class="bg-base-200  rounded-2xl text-center">{process.PID}</th>
                        <td class="bg-base-200  rounded-2xl text-center">{process.VPN}</td>
                        <td class="bg-base-200  rounded-2xl text-center">{process.PFN}</td>
                    </tr>
                {/each}
            </tbody>
        </table>
    </div>
</div>