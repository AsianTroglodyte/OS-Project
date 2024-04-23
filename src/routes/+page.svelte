<script>
    import ProcessList from "../components/ProcessList.svelte";
    import RamGrid from "../components/RAMGrid.svelte";
    import SwapSpace from "../components/SwapSpaceGrid.svelte";
    import LfuList from "../components/LFUList.svelte";
    import LruList from "../components/LRUList.svelte";
    import VirtualMemory from "../components/VirtualMemory.svelte";
    import NavBar from "../components/NavBar.svelte";

    let processes = [
        {PID: 1, VPN: "01", PFN: "01"},
        {PID: 2, VPN: "02", PFN: "02"},
        {PID: 3, VPN: "03", PFN: "03"},
        {PID: 4, VPN: "04", PFN: "04"},
    ]
</script>

<NavBar />

<!-- section for simulation -->
<div class="flex flex-row justify-center gap-10" >
    <div class = "flex flex-col justify-start overflow-y-auto" style="height: 80vh;">
        <ProcessList processes={processes}/>
        <LfuList processes={processes}/>
        <LruList processes={processes}/>
    </div>

    <div class="flex flex-row justify-center overflow-auto">
        <VirtualMemory />
        <span class = "text-7xl">&#8596;</span>
        <RamGrid />
        <span class = "text-7xl">&#8596;</span>
        <SwapSpace />
    </div>
</div>