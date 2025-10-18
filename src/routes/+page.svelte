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
    <ProjectDrawer {setCurApp} />
  </div>
{:else}
  <ProjectView />
{/if}
