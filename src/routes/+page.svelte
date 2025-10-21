<script lang="ts">
  import { onMount } from "svelte";
  import ProjectDrawer from "$lib/components/OpenAI/Project/Drawer.svelte";
  import ProjectView from "$lib/components/ProjectView.svelte";

  onMount(() => {});
  let apps = $state([]);
  let sortType = $state(1);
  let sortReversed = $state(0);
  let sortOptions = $state(["Alphabetical", "Date Created", "Times Opened"]);

  let curApp = $state(null);

  function setCurApp(_idx) {
    curApp = _idx;
  }
</script>

<!-- Project Drawer -->

{#if curApp == null}
  <div class="w-full h-full">
    <div class="flex flex-col bg-red-400 h-full">
      <div class="shrink p-2 flex items-center">
        <div class="flex-1 flex">
          <div class="bg-red-400 rounded-md flex-1">
            <div class="mb-2 flex flex-row gap-2">
              <label class="block text-sm font-medium flex-1">Sort</label>
              <select bind:value={sortType} class="w-full p-2 border rounded">
                {#each sortOptions as opt, idx}
                  <option value={idx} selected={idx == sortType}>{opt}</option>
                {/each}
              </select>
              <div class="mb-4 flex items-center gap-2">
                <input
                  id="reversed"
                  type="checkbox"
                  bind:checked={sortReversed}
                />
                <label for="reversed" class="text-sm">Reverse</label>
              </div>
            </div>
          </div>
        </div>
        <!--
        <div>
          <button class="p-2 bg-slate-400 rounded-md">Filter</button>
        </div>
-->
      </div>

      <div class="flex flex-1 bg-green-300 p-2">
        <div class="overflow-y-scroll relative flex-1 flex justify-center">
          <div class="flex justify-center flex-1 bg-red-400">
            <div
              class="absolute bg-blue-400 gap-2 grid grid-cols-[repeat(auto-fill,minmax(60px,1fr))] gap-4 rounded-md"
            >
              {#each Array(30) as app}
                <div class="bg-slate-100 size-[60px] rounded-md"></div>
              {/each}
            </div>
          </div>
        </div>
      </div>

      <div class="flex bg-blue-400 p-2">
        <div class="bg-red-400 rounded-md flex items-center h-[80px] w-full">
          <div class=" p-2 border-r-2 shrink border-dashed">
            <button class="bg-slate-300 rounded-md size-[60px]">+</button>
          </div>
          <div class="flex flex-1 justify-between p-2">
            {#each Array(3) as pin, idx}
              <button class="bg-slate-300 rounded-md size-[60px]">{idx}</button>
            {/each}
          </div>
        </div>
      </div>
      <div class="bg-yellow-300 w-full h-[40px] flex gap-1 p-2">
        {#each Array("Open", "Edit", "Delete") as opt}
          <button class="flex-1 bg-slate-300 rounded-md">{opt}</button>
        {/each}
      </div>
    </div>
    <!-- <ProjectDrawer {setCurApp} /> -->
  </div>
{:else}
  <ProjectView />
{/if}
