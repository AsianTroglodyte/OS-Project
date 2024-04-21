<script>
    const contents = [1,2,3,4,5,6,7,8,9,10]
</script>

<div class = "grid column">
    <div class = "text-center">
        Swap Space Grid
    </div>
    
    <div class="grid grid-rows-5 grid-cols-2 grid-flow-col bg-gradient-to-r from-purple-500 to-pink-500 gap-2 p-2">
        {#each contents as content}
            <div class="bg-fuchsia-500 w-40 h-16 text-white text-center content-center shadow-lg font-mono">{content}</div>
        {/each}
    </div>
</div>