<script lang="ts">
  import { onMount } from "svelte";
  import ProjectDrawer from "$lib/components/OpenAI/Project/Drawer.svelte";
  import ProjectView from "$lib/components/ProjectView.svelte";

  onMount(() => {});
  let apps = $state([]);

  let curApp = $state(null);

  function setCurApp(_idx) {
    curApp = _idx;
  }
</script>

<!-- Project Drawer -->

{#if curApp == null}
  <div class="w-full h-full border-r-2">
    <div class="flex flex-col bg-red-400 h-full">
      <div class="shrink p-2 flex items-center">
        <div class="flex-1">huh</div>
        <div>
          <button class="p-2 bg-slate-400 rounded-md">Filter</button>
        </div>
      </div>
      <div class="flex flex-1 bg-green-300 p-2">
        <div class="bg-slate-400 flex-1 rounded-md overflow-y-scroll"></div>
      </div>

      <div class="flex bg-blue-400 p-2">
        <div class="bg-red-400 rounded-md h-[80px] w-full"></div>
      </div>
      <div class="bg-yellow-300 w-full h-[40px]"></div>
    </div>
    <!-- <ProjectDrawer {setCurApp} /> -->
  </div>
{:else}
  <ProjectView />
{/if}
