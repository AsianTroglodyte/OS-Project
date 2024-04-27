<script lang="ts">
    import ProcessTable from "../components/ProcessTable.svelte";
    import PAS from "../components/PAS.svelte";
    import SwapSpace from "../components/SwapSpace.svelte";
    import LfuList from "../components/LFUList.svelte";
    import LruList from "../components/LRUList.svelte";
    import PageTable from "../components/PageTable.svelte";
    import NavBar from "../components/NavBar.svelte";
    import App from "../components/UnmappedProcesses.svelte";

    let processes = [
        {PID: 1, VPN: "01", PFN: 0, BlockID: "?", PresentBit: "0", ValidBit: "0"},
        {PID: 1, VPN: "02", PFN: 0, BlockID: "?", PresentBit: "0", ValidBit: "0"},
        {PID: 3, VPN: "03", PFN: 0, BlockID: "?", PresentBit: "0", ValidBit: "0"},
        {PID: 4, VPN: "04", PFN: 0, BlockID: "?", PresentBit: "0", ValidBit: "0"},
    ]

    let phys_addr_space_contents = 
        [{id: 0, name:"bruh"},
        {id:1, name:"bruh"},
        {id:2, name:"bruh"},
        {id:3, name:"bruh"},
        {id:4, name:"bruh"},
        {id:5, name:"bruh"},
        {id:6, name:"bruh"},
        {id:7, name:"bruh"},
        {id:8, name:"bruh"},
        {id:9, name:"bruh"}]

    let swap_space_contents = 
        [{id: 10, name:"bruh"},
        {id:11, name:"bruh"},
        {id:12, name:"bruh"},
        {id:13, name:"bruh"},
        {id:14, name:"bruh"},
        {id:15, name:"bruh"},
        {id:16, name:"bruh"},
        {id:17, name:"bruh"},
        {id:18, name:"bruh"},
        {id:19, name:"bruh"}]

</script>

<NavBar />

<!-- section for simulation -->

<div class = "flex flex-col items-center align center overflow-y-auto gap-5 m-3"> 
    <div class = "" style="width: 80vh;">
        bruh
    </div>


    <div class="flex flex-row justify-center" >
        <div class = "flex flex-col justify-start overflow-y-auto gap-5 m-3" style="height: 80vh;">
            <ProcessTable {processes}/>
            <LfuList {processes}/>
            <LruList {processes}/>
        </div>

        <div class="flex flex-row overflow-auto m-3">
            <PageTable {processes}/>
            <span class = "text-4xl">&#8596;</span>
            <PAS  phys_addr_space_contents={phys_addr_space_contents}/>
            <span class = "text-4xl">&#8596;</span>
            <SwapSpace swap_space_contents={swap_space_contents}/>
        </div>
    </div>
</div>
