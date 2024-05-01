<script>
    function refreshPage(){
        window.location.reload();
    }

    // function dropInfo (){
    //     infoDisplaying = !infoDisplaying;
    // }
</script>

<header class="flex navbar bg-base-100 flex-row" style="justify-content: space-between;">
    <h1 class="text-2xl font-semibold p-4" >Swap Space Simulator</h1>
    <div class="flex flex-row gap-4">
        <button 
            class="btn btn-primary p-4 bg-primary"
            on:click={refreshPage}>
            Info
        </button>
        <button 
            class="btn btn-primary p-4 bg-primary"
            on:click={refreshPage}>
            About
        </button>
        <button 
            class="btn btn-primary p-4 bg-secondary"
            on:click={refreshPage}>
            Reset
        </button>
    </div>

</header>