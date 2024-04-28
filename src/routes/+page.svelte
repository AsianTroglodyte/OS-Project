<script lang="ts">
    import ProcessTable from "../components/ProcessTable.svelte";
    import PAS from "../components/PAS.svelte";
    import SwapSpace from "../components/SwapSpace.svelte";
    import LfuList from "../components/LFUList.svelte";
    import LruList from "../components/LRUList.svelte";
    import PageTable from "../components/PageTable.svelte";
    import NavBar from "../components/NavBar.svelte";
    import App from "../components/UnmappedProcesses.svelte";

    // the processes list contains the info about processes and info on the pages of processes
        // IMPORTANT pageElemID. Elements must have unique ID's to work with DND functionality
        // + makes other operations extremely easy. Unfortunately, id 
    let processes = [
        {id: 0, processType: "P1"},
    ]

    // a separate list for pages is important for DND functionality because it's list items that get transferred across lists 
    let pageElems = [{id: 0, PID: 0, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]

    let pageElemID  = 1;

    let addProcess = (ProcessType: string) => {
        let newPID = 1;
        //  following determines the PID of latest process. Bit weird, but it is what it is
        for (let i = 1; processes.length >= i; i++) {
            // if no matching PID was found throughout process list
            if (processes.find((process) => process.id === i) !== undefined) {
                if (processes.length === i) {
                    newPID = i + 1;
                    break;
                }
                else {
                    continue;
                }
            }
            else if (processes.find((process) => process.id === i) === undefined) {
                newPID = i;
                break;
            }
        }

        // preparing the pages depending on ProcessType. could probably be more efficient but I'm too lazy
        //      NOTE: the following looks like a travesty along with pageElemID, but I think it's simple and effective  
        if (ProcessType === "P1") {
            pageElems = [...pageElems, {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}];
            pageElemID ++;
        }
        else if (ProcessType === "P2") {
            pageElems = [...pageElems, 
                        {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 1, PID: newPID, VPN:1, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
            pageElemID += 2;
        }
        else if (ProcessType === "P3") {
            pageElems = [...pageElems, 
                        {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 1, PID: newPID, VPN:1, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 2, PID: newPID, VPN:2, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
            pageElemID += 3; 
        }
    
        processes = [...processes, {id: newPID, processType: ProcessType}];
        console.log(processes);
        console.log(pageElems);
    }

    let removeProcess = (processID : number) => {
        // there's probably a better way to do this... too bad!
        // consider replacing with .find()
        for (let i = 0; processes.length > i; i++) {
            if (processes[i].id === processID) {
                processes.splice(i, 1);
                processes = processes;
                console.log(processes);
                break;
            }
        }
    }

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
            <ProcessTable {processes} {pageElems} {addProcess} {removeProcess}/>
            <LfuList {processes} {pageElems}/>
            <LruList {processes} {pageElems}/>
        </div>

        <div class="flex flex-row overflow-auto m-3">
            <PageTable {processes} {pageElems} />
            <span class = "text-4xl">&#8596;</span>
            <PAS  phys_addr_space_contents={phys_addr_space_contents}/>
            <span class = "text-4xl">&#8596;</span>
            <SwapSpace swap_space_contents={swap_space_contents}/>
        </div>
    </div>
</div>
