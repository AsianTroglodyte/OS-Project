<script lang="ts">
    import { fade } from "svelte/transition";
    // the default value is very important it allows for implicit 
    // typing. code refuses "any" 
    export let processes = [{PID: 0, VPN: "00", PFN: "00"}];
    
    let addProcess = () => {
        console.log("bruh");
        let newPID = 1;
        // weird function but it is what it is
        for (let i = 1; processes.length >= i; i++) {
            console.log("looping");
            // if no matching PID was found throughout process list
            if (processes.find((process) => process.PID === i) !== undefined) {
                console.log(processes.find((process) => process.PID === i));
                if (processes.length === i) {
                    newPID = i + 1;
                    break;
                }
                else {
                    continue;
                }
            }
            else if (processes.find((process) => process.PID === i) === undefined) {
                newPID = i;
                break;
            }
        }
    
        processes = [...processes, {PID: newPID, VPN: "bruh", PFN:"bruh"}];
        console.log(processes);
    }

    /*bad type safety. couldn't figure out this bit without this weird thing tbh*/
    let removeProcess = (processID : number) => {
        // there's probably a better way to do this... too bad!
        // consider replacing with .find()
        for (let i = 0; processes.length > i; i++) {
            if (processes[i].PID === processID) {
                processes.splice(i, 1);
                processes = processes;
                console.log(processes);
                break;
            }
        }
    }
    // for table border: border-separate border border-slate-500 
</script>



<div class="flex flex-col align-items gap-5">
    <!-- Process Label -->
    <div class="flex flex-row gap-5 justify-center">
        <h1 class="text-center">Process Table</h1>
        <button class="btn btn-xs btn-primary  bg-gradient-to-r from-sky-400 to-indigo-400" on:click={addProcess}>
            add
        </button>
    </div>

    <!-- Process list in the form of a table -->
    <div class="overflow-y-auto max-h-80 h-80 w-60
    border-separate border-2 border-slate-500 rounded-lg">
        <table class="table-xs table-auto w-full
        table-pin-rows table-pin-cols bg-base-200 ">

            <!-- head -->
            <thead>
                <tr>
                    <!-- rounded to make sure edges of table are not cut -->
                    <th class="bg-base-200  rounded-2xl text-xs">PID</th>
                    <th class="bg-base-200 text-xs"> VPN</th>
                    <th class="bg-base-200  rounded-2xl text-xs">PFN</th>
                    <th> </th>
                </tr>
            </thead>

            <!-- Body -->
            <tbody >
                {#each processes as process}
                    <tr transition:fade>
                        <!-- rounded to make sure edges of table are not cut -->
                        <th class="bg-base-200 rounded-2xl">{process.PID}</th>
                        <td class="text-center">{process.VPN}</td>
                        <td class="border-collapse border-slate-500 rounded-2xl" >
                            <p class="text-center">{process.PFN} </p> 
                        </td>
                        <td class="flex flex-row border-collapse border-slate-500 rounded-2xl gap-1">
                            <button class="btn btn-square btn-outline btn-sm"
                                on:click={() => removeProcess(process.PID)}>
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                            </button>
                            <button class="btn btn-square btn-outline btn-sm">
                                Run
                            </button>
                        </td>
                    </tr>
                {/each}
            </tbody>
        </table>
    </div>
</div>


