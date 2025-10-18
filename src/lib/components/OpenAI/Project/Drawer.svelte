<script>
  import { slide } from "svelte/transition";

  import ProjectGrid from "./Grid.svelte";
  import ProjectQuickBar from "./QuickBar.svelte";

  let showbar = $state(true);
  let projects = $state([]);
  let mode = $state(null);
  let selectedProject = $state(null);

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
    mode = "edit";
    selectedProject = project;
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
  }
</script>

<div class="relative w-full h-full bg-red-300 text-white">
  <ProjectGrid
    {projects}
    onAdd={handleAdd}
    onEdit={handleEdit}
    onCancel={closePanel}
  />
  {#if showbar}
    {#if mode}
      <div
        class="z-[9999] bottom-0 absolute w-full"
        transition:slide={{ y: 70, duration: 300 }}
      >
        huh
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
  {/if}
</div>
