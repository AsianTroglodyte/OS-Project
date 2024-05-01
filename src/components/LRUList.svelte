<script>
    // the default value is very important it allows for implicit 
    // typing. code refuses "any" 
    export let processes = [
        {id: 0, processType: "P1"},
    ]
    export let pageElems = [{id: 0, PID: 0, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
    // for borders on table: border-separate border border-slate-500 
</script>

<div class="flex flex-col gap-5 max-h-80">
    <h1 class = "text-center"> Least Recently Used</h1>
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
                {#each processes as process}
                    <tr>
                        <th class="bg-base-200  rounded-2xl text-center">{process.id}</th>
                        <td class="bg-base-200  rounded-2xl text-center">{process.processType}</td>
                    </tr>
                {/each}
            </tbody>
        </table>
    </div>
</div>