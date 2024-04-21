<script>
    import ProcessList from "../components/ProcessList.svelte";
    import RamGrid from "../components/RAMGrid.svelte";
    import SwapSpace from "../components/SwapSpaceGrid.svelte";
    import LfuList from "../components/LFUList.svelte";
    import LruList from "../components/LRUList.svelte";

    let processes = [
        {PID: 1, VPN: "01", PFN: "01"},
        {PID: 2, VPN: "02", PFN: "02"},
        {PID: 3, VPN: "03", PFN: "03"},
        {PID: 4, VPN: "04", PFN: "04"},
    ]
</script>

<!-- section for simulation -->
<div class = "flex flex-col justify-center gap-10">
    <div class = "flex flex-row gap-4 items-center justify-center">
        <ProcessList processes={processes}/>
        <RamGrid />
        <span class = "text-7xl">&#8596;</span>
        <SwapSpace />
    </div>


    <div class = "flex flex-row	 gap-4 items-center justify-center">
        <LfuList processes={processes}/>
        <LruList processes={processes}/>
    </div>
</div>