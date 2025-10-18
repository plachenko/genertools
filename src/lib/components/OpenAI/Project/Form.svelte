<script>
  let { mode, project, onSubmit, onCancel, onDelete } = $props();
  let name = $state(project?.name ?? "");
  let description = $state(project?.description ?? "");
  let txtColor = $state(project?.txtColor ?? "#FFFFFF"); // default blue
  let color = $state(project?.color ?? "#3b82f6"); // default blue
  let pin = $state(project?.pin ?? false);

  $effect(() => {
    if (project) {
      name = project?.name;
      description = project?.description;
      txtColor = project?.txtColor;
      color = project?.color;
      pin = project?.pin;
    }
  });

  function handleSubmit() {
    onSubmit?.({
      ...project,
      name: name || "Untitled Project",
      description,
      txtColor,
      color,
      pin,
      updatedAt: new Date().toISOString(),
    });
  }
</script>

<div
  class="bg-neutral-900/95 backdrop-blur-md text-white p-6 border-t border-neutral-800"
>
  <h2 class="text-2xl font-semibold mb-4 text-center">
    {mode === "create" ? "Create Project" : "Edit Project"}
  </h2>

  <form on:submit|preventDefault={handleSubmit} class="space-y-3">
    <div>
      <label class="text-sm text-neutral-400">Pin Project</label>
      <input
        bind:checked={pin}
        type="checkbox"
        class="w-full bg-neutral-800 border border-neutral-700 rounded-lg px-3 py-2 text-white focus:ring-2 focus:ring-blue-500"
      />
    </div>
    <div>
      <label class="text-sm text-neutral-400">Name</label>
      <input
        bind:value={name}
        class="w-full bg-neutral-800 border border-neutral-700 rounded-lg px-3 py-2 text-white focus:ring-2 focus:ring-blue-500"
        required
      />
    </div>

    <div>
      <label class="text-sm text-neutral-400">Description</label>
      <textarea
        bind:value={description}
        class="w-full bg-neutral-800 border border-neutral-700 rounded-lg px-3 py-2 text-white focus:ring-2 focus:ring-blue-500 resize-none"
      ></textarea>
    </div>

    <div class="flex flex-row">
      <div>
        <label class="text-sm text-neutral-400">Text Color</label>
        <input
          type="color"
          bind:value={txtColor}
          class="w-16 h-8 p-0 border border-neutral-600 rounded cursor-pointer"
        />
      </div>
      <div>
        <label class="text-sm text-neutral-400">Color</label>
        <input
          type="color"
          bind:value={color}
          class="w-16 h-8 p-0 border border-neutral-600 rounded cursor-pointer"
        />
      </div>
    </div>

    <div class="flex gap-2 pt-2">
      <button
        type="submit"
        class="flex-1 bg-blue-600 hover:bg-blue-500 py-2 rounded-lg"
      >
        {mode === "create" ? "Create" : "Save"}
      </button>
      {#if mode === "edit"}
        <button
          type="button"
          on:click={() => onDelete?.(project)}
          class="flex-1 bg-red-600 hover:bg-red-500 py-2 rounded-lg"
        >
          Delete
        </button>
      {/if}
      <button
        type="button"
        on:click={() => onCancel?.()}
        class="flex-1 bg-neutral-700 hover:bg-neutral-600 py-2 rounded-lg"
      >
        Cancel
      </button>
    </div>
  </form>
</div>
