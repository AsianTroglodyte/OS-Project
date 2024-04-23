<script>
    const contents = [0,1,2,3,4,5,6,7,8,9]
</script>

<div class="flex flex-col gap-5">
    <div class="text-center">
        Swap Space / Disk Pages
    </div>
    
    <div class="grid grid-rows-5 grid-cols-2 grid-flow-col bg-gradient-to-r bg-gradient-to-r from-sky-500 to-indigo-600 gap-2 p-2 rounded">
        {#each contents as content, PFN}
            <div class="bg-primary w-28 h-20 text-white shadow-lg font-mono rounded p-2">
                <p class="text-xs">PFN:{PFN}</p>
                <p class="text-xs">PID: ?!?!</p>
                <p class="text-xs">!?!?</p>
                <p class="text-xs">!?!?</p> 
            </div>
        {/each}
    </div>
</div>