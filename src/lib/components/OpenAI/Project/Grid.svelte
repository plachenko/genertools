<script>
  import { slide } from "svelte/transition";

  import ProjectButton from "./Button.svelte";

  let { projects = [], onAdd, onCancel, onEdit } = $props();
  let container;

  let groupSize = $state(4);
  let containerWidth = $state(0);
  let selectedIdx = $state(null);

  let sortType = $state(2); // 0: alphabetical, 1: timesOpened, 2: date created
  let sortReversed = $state(false);

  let showFilter = $state(false);

  function selectProj(idx) {
    if (selectedIdx == idx) {
      selectedIdx = null;
      onCancel();
      return;
    }
    selectedIdx = idx;

    onEdit(projects[idx]);
  }

  /**
   * Sorts an array of objects by a chosen criterion.
   *
   * @param {Array} items - The array to sort.
   * @param {number} sortType - The sort mode:
   *   0 → alphabetical (default)
   *   1 → timesOpened (descending)
   *   2 → date created (ascending)
   * @param {boolean} reversed - Whether to reverse the result.
   */
  function sortItems(items, sortType = 0, reversed = false) {
    const sorted = [...items].sort((a, b) => {
      switch (sortType) {
        // 0 → alphabetical (case-insensitive)
        case 0: {
          const nameA = (a.name || "").toLowerCase();
          const nameB = (b.name || "").toLowerCase();
          if (nameA < nameB) return -1;
          if (nameA > nameB) return 1;
          return 0;
        }

        // 1 → timesOpened (descending)
        case 1: {
          const openA = a.timesOpened ?? 0;
          const openB = b.timesOpened ?? 0;
          return openB - openA;
        }

        // 2 → created date (ascending)
        case 2: {
          const dateA = new Date(a.created || 0);
          const dateB = new Date(b.created || 0);
          return dateA - dateB;
        }

        default:
          return items;
      }
    });

    return reversed ? sorted.reverse() : sorted;
  }

  // Dynamically adjust group size based on container width
  function updateGroupSize(width) {
    containerWidth = width;
    if (width < 480)
      groupSize = 2; // very small
    else if (width < 800)
      groupSize = 3; // medium
    else groupSize = 4; // wide
  }

  $effect(() => {
    if (!container) return;
    if (projects) {
    }
    const observer = new ResizeObserver((entries) => {
      for (const entry of entries) {
        updateGroupSize(entry.contentRect.width);
      }
    });
    observer.observe(container);
    return () => observer.disconnect();
  });

  function groupedProjects() {
    const groups = [];
    for (let i = 0; i < projects.length; i += groupSize) {
      groups.push(projects.slice(i, i + groupSize));
    }
    return groups;
  }

  function overflowProjects() {
    const remainder = projects.length % groupSize;
    if (remainder === 0) return [];
    return projects.slice(-remainder);
  }
</script>

<!-- Outer container -->
<div
  bind:this={container}
  class="relative w-full h-full overflow-hidden bg-neutral-900"
>
  <button
    class="bg-slate-400 p-2 rounded-md"
    onclick={() => {
      showFilter = !showFilter;
    }}>Filter</button
  >
  {#if showFilter}
    <div
      transition:slide={{ y: 100, duration: 500 }}
      class="bg-red-400 p-3 border rounded-md max-w-md"
    >
      <div class="mb-2">
        <label class="block text-sm font-medium mb-1">Sort type</label>
        <select bind:value={sortType} class="w-full p-2 border rounded">
          <option value="0">Alphabetical</option>
          <option value="1">Times opened</option>
          <option value="2">Date created</option>
        </select>
      </div>

      <div class="mb-4 flex items-center gap-2">
        <input id="reversed" type="checkbox" bind:checked={sortReversed} />
        <label for="reversed" class="text-sm">Reverse final order</label>
      </div>
    </div>
  {/if}

  <!-- Scrollable project grid -->
  <div class="absolute inset-0 flex flex-col overflow-y-auto p-4 space-y-4">
    <div class="bg-neutral-800 rounded-md flex-1 relative overflow-y-scroll">
      <div
        class="select-none text-neutral-500/80 flex items-center justify-center h-full italic"
      >
        {#if !projects.length}
          + add a project
        {/if}
      </div>
      <div
        class="absolute top-0 w-full pt-4 flex justify-center gap-2 flex-wrap"
      >
        <div class="flex w-[80%] gap-2 items-start flex-wrap">
          {#each sortItems(projects, sortType, sortReversed) as project, idx}
            <ProjectButton
              {project}
              selected={selectedIdx == idx}
              onClick={() => selectProj(idx)}
              class="size-20 shrink-0"
            />
          {/each}
        </div>
      </div>
      <!-- Yellow overflow section -->
      <!--
      {#if overflowProjects().length > 0}
        {#each overflowProjects() as project}
          <ProjectButton {project} onClick={onEdit} class="size-20 shrink-0" />
        {/each}
      {/if}
-->
    </div>

    <!--
    {#each groupedProjects() as group}
      <div class="relative bg-neutral-800/40 p-3 rounded-xl flex gap-4">
        <ProjectButton
          project={{ name: "+", color: "#333" }}
          onClick={onAdd}
          class="size-20"
        />

        <div class="absolute inset-0 pointer-events-none">
          <div class="absolute bottom-0 left-0 flex flex-wrap gap-4 p-3">
            {#each group as project}
              <ProjectButton
                {project}
                onClick={onEdit}
                class="size-20 pointer-events-auto"
              />
            {/each}
          </div>
        </div>
      </div>
    {/each}
  -->

    <div
      class="border-neutral-100/20 border-2 bg-neutral-800/40 p-3 rounded-xl flex gap-4"
    >
      <ProjectButton
        project={{ name: "+", color: "#333" }}
        onClick={onAdd}
        class="size-20"
      />
      {#each projects.filter((e) => e.pin == true).slice(0, 3) as pinned}
        <ProjectButton
          quickBtn={true}
          project={pinned}
          onClick={() => selectProj(idx)}
          class="size-20"
        />
      {/each}
    </div>
  </div>
</div>
