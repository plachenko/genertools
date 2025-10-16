<script>
  import ProjectForm from "./Form.svelte";
  let { mode, project, onSubmit, onCancel, onDelete, onDeleteAll } = $props();

  let showConfirm = $state(false);
</script>

<!-- Quickbar container: slide-in animation only -->
<div class="fixed bottom-0 left-0 w-full z-50 animate-slide-in">
  <ProjectForm {mode} {project} {onSubmit} {onCancel} {onDelete} />

  <!-- Manage area with Remove All -->
  <div
    class="bg-neutral-950/95 backdrop-blur-md border-t border-neutral-800 text-white py-3 px-6 flex justify-between items-center"
  >
    <span class="text-sm text-neutral-400">Manage Projects</span>
    <button
      on:click={() => (showConfirm = true)}
      class="bg-red-700 hover:bg-red-600 px-4 py-1 rounded-md text-sm"
    >
      Remove All
    </button>
  </div>

  <!-- Confirmation modal -->
  {#if showConfirm}
    <div
      class="fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center z-[60] animate-fade-in"
    >
      <div
        class="bg-neutral-900 border border-neutral-700 rounded-lg p-6 w-80 text-center space-y-4"
      >
        <h3 class="text-lg font-semibold text-white">Delete All Projects?</h3>
        <p class="text-sm text-neutral-400">This action cannot be undone.</p>
        <div class="flex justify-center gap-3">
          <button
            on:click={() => {
              onDeleteAll?.();
              showConfirm = false;
            }}
            class="bg-red-600 hover:bg-red-500 px-4 py-2 rounded-md text-white font-medium"
          >
            Delete All
          </button>
          <button
            on:click={() => (showConfirm = false)}
            class="bg-neutral-700 hover:bg-neutral-600 px-4 py-2 rounded-md text-white font-medium"
          >
            Cancel
          </button>
        </div>
      </div>
    </div>
  {/if}
</div>

<style>
  @keyframes slide-in {
    from {
      transform: translateY(100%);
      opacity: 0;
    }
    to {
      transform: translateY(0);
      opacity: 1;
    }
  }
  @keyframes fade-in {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  .animate-slide-in {
    animation: slide-in 0.25s ease-out forwards;
  }
  .animate-fade-in {
    animation: fade-in 0.15s ease-out forwards;
  }
</style>
