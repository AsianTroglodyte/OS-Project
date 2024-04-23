<script>
    const contents = [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,18]
</script>

<div class="flex flex-col gap-5 ">
    <div class="text-center">
        Virtual Memory Pages
    </div>
    
    <div class="overflow-y-auto" style="height:74vh" >
        <div class="grid grid-rows-9 grid-cols-2 grid-flow-col box-border
        bg-gradient-to-r bg-gradient-to-r 
        from-sky-500 to-indigo-600 
        gap-2 p-2 overflow-y-auto 
        rounded ">
                {#each contents as content, VPN}
                <!-- for centering content: text-center content-center -->
                    <div class="bg-primary  box-border w-28 h-20 text-white shadow-lg font-mono rounded p-2">
                        <p class="text-xs">VPN:{VPN}</p>
                        <p class="text-xs">PID: ?!?!</p>
                        <p class="text-xs">!?!?</p>
                        <p class="text-xs">!?!?</p> 
                    </div>
                {/each}
        </div>
    </div>
</div>