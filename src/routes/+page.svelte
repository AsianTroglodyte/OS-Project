<script>
    import ProcessTableAndStats from "../components/ProcessTableAndStats.svelte";
    import PAS from "../components/MemoryVisualizations/PAS.svelte";
    import SwapSpace from "../components/MemoryVisualizations/SwapSpace.svelte";
    import PageTable from "../components/MemoryVisualizations/PageTable.svelte";
    import NavBar from "../components/NavBar.svelte";
    import UserPromptArea from "../components/UserPromptArea.svelte";


    // for process table. Need to define shape using TS: {id: 0, processType: "P1"}
    // note that id here is the same as PID. I should make changes for consistency
    let processes = [];
    // for page table need to define shape of data using TS: [{id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}]
    let pageElems = [];
    // I don't remember why this is here, but its probably important
    let pageElemID  = 1;
    // this indicates pages needed to run a process by VPN
    let pagesNeededVPNs = []; 
    // indicates Process that is running by PID
    let curRunningProcessID = -1;
    // state for changing UserPromptArea
    let state;

    // performs all tasks related to adding a process
    let addProcess = (ProcessType) => {
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
        //      NOTE: the following is sort of a travesty but it is flexible.
        if (ProcessType === "P1") {
            pageElems = [...pageElems, {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 1, PID: newPID, VPN:1, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0}
            ];
            pageElemID += 2;
        }
        else if (ProcessType === "P2") {
            pageElems = [...pageElems, 
                        {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 1, PID: newPID, VPN:1, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 2, PID: newPID, VPN:2, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 3, PID: newPID, VPN:3, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0}]
            pageElemID += 4;
        }
        else if (ProcessType === "P3") {
            pageElems = [...pageElems, 
                        {id: pageElemID, PID: newPID, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 1, PID: newPID, VPN:1, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 2, PID: newPID, VPN:2, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 3, PID: newPID, VPN:3, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 4, PID: newPID, VPN:4, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0},
                        {id: pageElemID + 5, PID: newPID, VPN:5, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0, Frequency: 0}]
            pageElemID += 6; 
        }
        processes = [...processes, {id: newPID, processType: ProcessType}];
    }


    // performes all tasks related to removing a process: removing all related pages from memory
    let removeProcess = (processID) => {
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

        // if you delete a process that is running
        if (curRunningProcessID === processID) {
            curRunningProcessID = -1;
            pagesNeededVPNs = [];
        }
    }



    // Runs a Process and prompts users to manage pages by highlighting the randomly chosen relevant pages. all pages are 
    // supposed to be in the physical address space to run. When it is run, frequency and recency of use for a page are updated and 
    // the process takes its time to actually run. 
    function getRandomInt(max) {
        return Math.floor(Math.random() * max);
    }

    let promptRunProcess = (processID) => {
        // only allow this to run if there isn't already a process running
        if (curRunningProcessID !== -1) {
            return;
        }
        // finding processs to run
        let runningProcessIndex = processes.findIndex(process => process.id === processID);

        // pages vary depending on process
        if (processes[runningProcessIndex].processType === "P1") pagesNeededVPNs = [{VPN: 0, inPAS: false},{VPN: 1, inPAS: false}];
        else if (processes[runningProcessIndex].processType === "P2") pagesNeededVPNs = [{VPN: 0, inPAS: false}, {VPN: 1, inPAS: false}, 
                                                                                        {VPN: 2, inPAS: false}, {VPN: 3, inPAS: false}];
        else if (processes[runningProcessIndex].processType === "P3") pagesNeededVPNs = [{VPN: 0, inPAS: false}, {VPN: 1, inPAS: false},
                                                                                        {VPN: 2, inPAS: false}, {VPN: 3, inPAS: false},
                                                                                        {VPN: 4, inPAS: false}, {VPN: 5, inPAS: false}];

        // randomly choosing the amount of processes to not run
        let numItemsToRemove = 1;
        if (processes[runningProcessIndex].processType !== "P1") {
            numItemsToRemove = getRandomInt(3);
        }

        // randomly removing pages that are not needed to run from pagesNeededVPNs
        for (let i = 0; i < numItemsToRemove; i ++) {
            let randIndex = getRandomInt(pagesNeededVPNs.length);
            pagesNeededVPNs.splice(randIndex, randIndex);
        }
        
        // need to change the prompt area using state
        state = "waiting for pages";
        
        // update the current running process
        curRunningProcessID = processID;
    }

    // returns true if PAS contains all pages required for current running process  
    function runProcess() {
        state = "running process";

        function wipeEverything() {
            // order here is important some things respond in reaction to change of curRunningProcessID that
            // needs info from pagesNeededVPNs
            curRunningProcessID = -1;

            pagesNeededVPNs = [];

            // changing process back to initial state for promptArea
            state = "waiting for run";
        }

        setTimeout(wipeEverything, 3000);
    }


    // Bits displayed on page table need to be changed depending on whether it is in: memory and or disk
    let changePBit = (PID, VPN, changeNum) => {
        let changingElemIndex = pageElems.findIndex(pageElem => pageElem.PID === PID  && pageElem.VPN === VPN);
        pageElems[changingElemIndex].PresentBit = changeNum;
    }
    let changeVBit = (PID, VPN, changeNum) => {
        let changingElemIndex = pageElems.findIndex(pageElem => pageElem.PID === PID  && pageElem.VPN === VPN);
        pageElems[changingElemIndex].ValidBit = changeNum;
    }

</script>

<NavBar />

<!-- section for simulation -->
<div class = "flex flex-col items-center overflow-y-auto "> 
    <!-- <div class = "" style="width: 80vh;">
        <p>NOTE: Page Table valid bit indicates whether a page has been allocated any memory OR disk of any stroke-width.
        In other words, a "true" valid bit indicates means that the page is in either memory or disk. </p>

        <p>Present Bit, on the other hand is indicates whether the page is in EITHER memory OR disk. In other words, if 
        the present bit is true (1) that means the page is in memory and false (0) if it is disk instead. </p>
    </div> -->
    <UserPromptArea {state} {processes}/>

    
    <div class="flex flex-row justify-center" >
        <div class = "flex flex-col justify-start overflow-y-auto gap-5 m-3" style="height: 74vh;">
            <ProcessTableAndStats {processes} {addProcess} {removeProcess} {promptRunProcess}/>
        </div>

        <div class="flex flex-row overflow-auto m-3">
            <PageTable {pageElems} {processes} {pagesNeededVPNs} {curRunningProcessID}/>
            <span class = "text-4xl">&#8596;</span>
            <!-- Yes, yes binds are very bad. I definitely need to better understand state management tools here. including in react -->
            <PAS  {changePBit} {changeVBit} {processes} {curRunningProcessID} bind:pagesNeededVPNs={pagesNeededVPNs} {runProcess} {state}/>
            <span class = "text-4xl">&#8596;</span>
            <SwapSpace {changePBit} {changeVBit} {processes} {curRunningProcessID} bind:pagesNeededVPNs={pagesNeededVPNs} {state}/>
        </div>
    </div>
</div>