<script>
    function refreshPage(){
        window.location.reload();
    } 
</script>

<header class="flex navbar bg-base-100 flex-row" style="justify-content: space-between;">
    <a class="text-2xl font-semibold p-4" >Swap Space Simulator</a>
    <button 
        class="btn btn-primary p-4 bg-gradient-to-r from-violet-500 to-fuchsia-500"
        on:click={refreshPage}>
        Reset
    </button>
</header>