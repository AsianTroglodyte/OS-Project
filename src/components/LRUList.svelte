<script>
    // the default value is very important it allows for implicit 
    // typing. code refuses "any" 
    export let processes = [{PID: 0, VPN: "00", PFN: "00"}];
    // for borders on table: border-separate border border-slate-500 
</script>

<div class="flex flex-col gap-5 m-3 max-h-80 h-80">
    <h1 class = "text-center"> Least Recently Used</h1>
    <div class="overflow-y-auto max-h-80 h-80 w-60
    border-separate border-2 border-slate-500 rounded-lg">
        <table class="table table-auto bg-base-200">
            
            <!-- head -->
            <thead>
                <tr class = "w-24">
                    <th>PID</th>
                    <th>VPN</th>
                    <th>PFN</th>
                </tr>
            </thead>
            <tbody>
                {#each processes as process}
                    <tr>
                        <th class="bg-base-200  rounded-2xl">{process.PID}</th>
                        <td class="bg-base-200  rounded-2xl">{process.VPN}</td>
                        <td class="bg-base-200  rounded-2xl">{process.PFN}</td>
                    </tr>
                {/each}
            </tbody>
        </table>
    </div>
</div>