<script lang="ts">
    import ProcessTable from "../components/ProcessTable.svelte";
    import PAS from "../components/PAS.svelte";
    import SwapSpace from "../components/SwapSpace.svelte";
    import LfuList from "../components/LFUList.svelte";
    import LruList from "../components/LRUList.svelte";
    import PageTable from "../components/PageTable.svelte";
    import NavBar from "../components/NavBar.svelte";

    // defining different lists for each table simplay makes things a bit easier to implement 
    // Perhaps makes using the tables a bit more difficult makes other operations extremely easy.
    let processes = [
        {id: 0, processType: "P1"},
    ]
    let pageElems = [{id: 0, PID: 0, VPN:0, PFN:0, BlockID: 0, PresentBit: 0, ValidBit: 0}];



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
        
        // for () pageElems
        // 
    }

    let changePBit = (PID : number, VPN : number, changeNum : number) => {
        let changingElemIndex = pageElems.findIndex(pageElem => pageElem.PID === PID  && pageElem.VPN === VPN);
        pageElems[changingElemIndex].PresentBit = changeNum;
    }
    let changeVBit = (PID : number, VPN : number, changeNum : number) => {
        let changingElemIndex = pageElems.findIndex(pageElem => pageElem.PID === PID  && pageElem.VPN === VPN);
        pageElems[changingElemIndex].ValidBit = changeNum;
    }
    // checks if there is a copy of a memory address
    let copyChecker = (PID : number, VPN : number, changeNum : number) => {
        let changingElemIndex = pageElems.findIndex(pageElem => pageElem.PID === PID  && pageElem.VPN === VPN);
        if (changingElemIndex) {
            return true;
        }
    }

    let printPageElems = () => {
        console.log(pageElems);
    }

    let modifyPageElems = (func) => {
        func(pageElems);
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
            <ProcessTable {processes} {addProcess} {removeProcess}/>
            <LfuList {processes} {pageElems}/>
            <LruList {processes} {pageElems}/>
        </div>

        <div class="flex flex-row overflow-auto m-3">
            <PageTable {pageElems} {printPageElems}/>
            <span class = "text-4xl">&#8596;</span>
            <PAS  {changePBit} {changeVBit}/>
            <span class = "text-4xl">&#8596;</span>
            <SwapSpace {changePBit} {changeVBit}/>
        </div>
    </div>
</div>