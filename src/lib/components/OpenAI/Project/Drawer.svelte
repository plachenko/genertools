<script>
  import { slide } from "svelte/transition";

  import ProjectGrid from "./Grid.svelte";
  import ProjectQuickBar from "./QuickBar.svelte";

  let showbar = $state(true);
  let projects = $state([]);
  let mode = $state(null);
  let selectedProject = $state(null);
  let activeButton = $state(null); // New: Tracks selected button

  // Load/save
  $effect(() => {
    const saved = localStorage.getItem("projects");
    if (saved) projects = JSON.parse(saved);
  });
  $effect(() => {
    localStorage.setItem("projects", JSON.stringify(projects));
  });

  function handleAdd() {
    mode = "create";
    selectedProject = null;
  }

  function handleEdit(project) {
    if (project.name === "+") return handleAdd();
    // mode = "edit";
    selectedProject = project;
    // activeButton = "edit";
  }

  function handleSubmit(project) {
    if (mode === "create") {
      projects = [...projects, { ...project, id: crypto.randomUUID() }];
    } else if (mode === "edit") {
      projects = projects.map((p) => (p.id === project.id ? project : p));
    }
    closePanel();
  }

  function handleDelete(project) {
    projects = projects.filter((p) => p.id !== project.id);
    closePanel();
  }

  function handleDeleteAll() {
    if (confirm("Are you sure you want to remove all projects?")) {
      projects = [];
      closePanel();
    }
  }

  function closePanel() {
    mode = null;
    selectedProject = null;
    activeButton = null; // Reset active button
  }

  function handleBarClick(button) {
    activeButton = button;

    if (button === "edit") {
      mode = "edit";
    } else {
      mode = null;
    }
  }
</script>


<div class="relative w-full h-full bg-red-300 text-white">
  <ProjectGrid
    {projects}
    onAdd={handleAdd}
    onEdit={handleEdit}
    onCancel={closePanel}
  />
  {#if showbar && selectedProject}
  <div class="absolute bottom-0 left-0 w-full bg-black/30 p-2 flex justify-center gap-4 z-40">
    {#each ["open", "edit", "delete"] as button}
      <button
        class="px-4 py-2 rounded-md text-sm font-semibold transition-all duration-150
                hover:bg-white hover:text-black
                {activeButton === button ? 'bg-white text-black' : 'bg-black text-white'}"
        on:click={() => handleBarClick(button)}
      >
        {button.charAt(0).toUpperCase() + button.slice(1)}
      </button>
    {/each}
  </div>
{/if}

   {#if showbar && activeButton === "edit" && mode === "edit"}
  <div
    class="z-[9999] bottom-12 absolute w-full"
    transition:slide={{ y: 70, duration: 300 }}
  >
    <ProjectQuickBar
      {mode}
      project={selectedProject}
      onSubmit={handleSubmit}
      onCancel={closePanel}
      onDelete={handleDelete}
      onDeleteAll={handleDeleteAll}
    />
  </div>
{/if}

</div>
