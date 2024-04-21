<script>
    const contents = [1,2,3,4,5,6,7,8,9,10]
</script>

<div class = "grid column gap-5 m-3">
    <div class = "text-center">
        Swap Space / Disk
    </div>
    
    <div class="grid grid-rows-5 grid-cols-2 grid-flow-col bg-gradient-to-r from-sky-500 to-indigo-600 gap-2 p-2 rounded">
        {#each contents as content}
            <div class="bg-primary w-32 h-14 text-white text-center content-center shadow-lg font-mono rounded">{content}</div>
        {/each}
    </div>
</div>