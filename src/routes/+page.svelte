<script lang="ts">
    import ProcessList from "../components/ProcessList.svelte";
    import RamGrid from "../components/RAMGrid.svelte";
    import SwapSpace from "../components/SwapSpaceGrid.svelte";
    import LfuList from "../components/LFUList.svelte";
    import LruList from "../components/LRUList.svelte";
    import VirtualMemory from "../components/VirtualMemory.svelte";
    import NavBar from "../components/NavBar.svelte";
    import App from "../components/App.svelte";

    let processes = [
        {PID: 1, VPN: "01", PFN: "01"},
        {PID: 2, VPN: "02", PFN: "02"},
        {PID: 3, VPN: "03", PFN: "03"},
        {PID: 4, VPN: "04", PFN: "04"},
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

    const handleConsider = (evt : CustomEvent<DndEvent<{id: number; name: string;}>>) => {
        console.log("consider");
        phys_addr_space_contents = evt.detail.items;
        swap_space_contents = evt.detail.items;
        
    }

    const handlefinalize = (evt : CustomEvent<DndEvent<{id: number; name: string;}>>) => {
        console.log("consider");
        phys_addr_space_contents = evt.detail.items;
        swap_space_contents = evt.detail.items;
    }

</script>

<NavBar />

<!-- section for simulation -->
<div class="flex flex-row justify-center" >
    <div class = "flex flex-col justify-start overflow-y-auto gap-5 m-3" style="height: 80vh;">
        <ProcessList {processes}/>
        <LfuList {processes}/>
        <LruList {processes}/>
    </div>

    <div class="flex flex-row overflow-auto m-3">
        <VirtualMemory />
        <span class = "text-4xl">&#8596;</span>
        <RamGrid 
            phys_addr_space_contents={phys_addr_space_contents}
            swap_space_contents={swap_space_contents}/>
        <span class = "text-4xl">&#8596;</span>
        <SwapSpace 
            phys_addr_space_contents={phys_addr_space_contents}
            swap_space_contents={swap_space_contents}/>
    </div>
</div>