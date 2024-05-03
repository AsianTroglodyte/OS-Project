<script lang="ts">
    import ProcessTableAndStats from "../components/ProcessTableAndStats.svelte";
    import PAS from "../components/MemoryVisualizations/PAS.svelte";
    import SwapSpace from "../components/MemoryVisualizations/SwapSpace.svelte";
    import PageTable from "../components/MemoryVisualizations/PageTable.svelte";
    import NavBar from "../components/NavBar.svelte";


    // for process table. Need to define shape using TS: {id: 0, processType: "P1"}
    // note that id here is the same as PID. I should make changes for consistency
    let processes = [];
    // for page table need to define shape of data using TS: [{id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
    let pageElems = [];
    // I don't remember why this is here, but its probably important
    let pageElemID  = 1;



    // performs all tasks related to adding a process
    let addProcess = (ProcessType: string) => {
        let newPID = 0;
        //  following determines the PID of latest process. Bit weird, but it is what it is
        for (let i = 0; processes.length >= i; i++) {
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

        // appending pageElems pages () depending on ProcessType. could probably be more efficient but I'm too lazy
        //      NOTE: the following looks like a travesty along with pageElemID, but I think it's simple and effective  
        if (ProcessType === "P1") {
            pageElems = [...pageElems, {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}];
            pageElemID ++;
        }
        else if (ProcessType === "P2") {
            pageElems = [...pageElems, 
                        {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 1, PID: newPID, VPN:1, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 2, PID: newPID, VPN:2, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
            pageElemID += 3;
        }
        else if (ProcessType === "P3") {
            pageElems = [...pageElems, 
                        {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 1, PID: newPID, VPN:1, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 2, PID: newPID, VPN:2, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 3, PID: newPID, VPN:3, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0},
                        {id: pageElemID + 4, PID: newPID, VPN:4, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
            pageElemID += 5; 
        }
        processes = [...processes, {id: newPID, processType: ProcessType}];
        // console.log("add PageElems: ",pageElems)
        // console.log("add processes: ",processes)
    }



    // performes all tasks related to removing a process: removing all related pages from memory
    let removeProcess = (processID : number) => {
        // removes items from process list. there's probably a better way to do this... too bad! 
        // I swear I tried using find indexOf and my implementations kept breaking. I'll stick with this for now.
        // maybe it is because I previously had a faulty implementation of PTE. Should try later.
        for (let i = processes.length - 1; 0 <= i; i--) {
            if (processes[i].id === processID) {
                processes.splice(i, 1);
                processes = processes;
                break;
            }
        }

        // removes items from pageElems. 
        for (let i = pageElems.length - 1; 0 <= i; i--) {
            if (pageElems[i].PID === processID) {
                pageElems.splice(i, 1);
                pageElems = pageElems;
            }
        }
        // console.log("add PageElems: ",pageElems)
        // console.log("add processes: ",processes)
    }



    // Runs a Process and prompts users to manage pages so that all relevant pages are in the physical
    // address space to run. When it is run, frequency and recency of use for a page are updated. 
    function getRandomInt(max) {
        return Math.floor(Math.random() * max);
    }
    // let runProcess = (processes) => {
    //     let runningProcessIndex = getRandomInt(processes.length);
    //     let runningProcessPID = processes.find(process => );

    //     console.log("running", );
    // } 



    // Bits displayed on page table need to be changed depending on whether it is in: memory and or disk
    let changePBit = (PID : number, VPN : number, changeNum : number) => {
        let changingElemIndex = pageElems.findIndex(pageElem => pageElem.PID === PID  && pageElem.VPN === VPN);
        pageElems[changingElemIndex].PresentBit = changeNum;
    }
    let changeVBit = (PID : number, VPN : number, changeNum : number) => {
        let changingElemIndex = pageElems.findIndex(pageElem => pageElem.PID === PID  && pageElem.VPN === VPN);
        pageElems[changingElemIndex].ValidBit = changeNum;
    }
</script>

<NavBar />

<!-- section for simulation -->
<div class = "flex flex-col items-center align center overflow-y-auto gap-5 m-3"> 
    <!-- <div class = "" style="width: 80vh;">
        <p>NOTE: Page Table valid bit indicates whether a page has been allocated any memory OR disk of any stroke-width.
        In other words, a "true" valid bit indicates means that the page is in either memory or disk. </p>

        <p>Present Bit, on the other hand is indicates whether the page is in EITHER memory OR disk. In other words, if 
        the present bit is true (1) that means the page is in memory and false (0) if it is disk instead. </p>
    </div> -->

    <div class="flex flex-row justify-center" >
        <div class = "flex flex-col justify-start overflow-y-auto gap-5 m-3" style="height: 80vh;">
            <ProcessTableAndStats {processes} {addProcess} {removeProcess}/>
        </div>

        <div class="flex flex-row overflow-auto m-3">
            <PageTable {pageElems} {processes}/>
            <span class = "text-4xl">&#8596;</span>
            <PAS  {changePBit} {changeVBit} {processes}/>
            <span class = "text-4xl">&#8596;</span>
            <SwapSpace {changePBit} {changeVBit} {processes}/>
        </div>
    </div>
</div>