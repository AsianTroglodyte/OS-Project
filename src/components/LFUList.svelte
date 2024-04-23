<script>
    // the default value is very important it allows for implicit 
    // typing. code refuses "any" 
    export let processes = [{PID: 0, VPN: "00", PFN: "00"}];
</script>

<div class="flex flex-col gap-5 max-h-80 h-80">
    <h1 class = "text-center"> Least Frequently Used</h1>
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
                        <th class="text-center">{process.PID + 1}</th>
                        <td class="text-center">{process.VPN}</td>
                        <td class="text-center">{process.PFN}</td>
                    </tr>
                {/each}
            </tbody>
        </table>
    </div>
</div>