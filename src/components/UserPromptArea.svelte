<script>
    import { tweened} from "svelte/motion";
    import {cubicOut} from "svelte/easing";
    
    export let state = "waiting for run";
    export let processes;
    
    // need to learn more about tweened
    let progress = tweened(0, {})

    function runProgressBar() {
        if (state === "running process") {
            console.log("running", progress);
            progress = tweened(0, {
                duration: 2500,
                easing: cubicOut,
            })
            progress.set(100);
        }
    }
    $: state, runProgressBar()
</script>

<div class = "flex flex-row justify-center  overflow-y-auto gap-4 h-6" style="width: 600px;">
    <!-- <p>NOTE: Page Table valid bit indicates whether a page has been allocated any memory OR disk of any stroke-width.
    In other words, a "true" valid bit indicates means that the page is in either memory or disk. </p> -->

    {#if state === "running process"}
        <p class="text-sm font-medium flex flex-col justify-center">Running: </p>
        <div class="flex flex-col justify-center">
            <progress class="progress progress-accent w-56 h-2" value="{$progress}" max="100"></progress>
        </div>
    {:else if state === "waiting for run" && processes.length > 0}
        <!-- color illusions go brrrr -->
        <p class="text-sm font-medium	"> Run a <span class="" style="color: rgb(140 155 227);">process</span></p>
    {:else if state === "waiting for run" && processes.length === 0}
        <!-- color illusions go brrrr -->
        <p class="text-sm font-medium	"> Add then run a <span class="" style="color: rgb(140 155 227);">process</span></p>
    {:else if state === "waiting for pages"}
        <!-- <p class="">move/allocate all <span class="text-teal-400">required pages</span> for into the Physical Address Spaces</p> -->
        <p class="text-sm font-medium	">Move/allocate all <span class="text-teal-400"> required pages</span> for into the Physical Address Spaces</p>
    {/if}
</div>