<script>
  import { fade } from "svelte/transition";
  let { project, onClick, selected, quickBtn = false } = $props();
</script>

<button
  class={`size-20 ${selected ? "border-white border-2" : ""} rounded-lg relative flex items-center justify-center font-semibold cursor-pointer transition-transform hover:scale-105 select-none`}
  style={`background-color: ${project.color || "#444"}; color:${project.txtColor || "#FFF"}`}
  onclick={() => onClick?.(project)}
>
  {#if project.pin && !quickBtn}
    <div
      transition:fade
      class="border-2 border-black/40 bg-red-400 rounded-full absolute right-1 top-1 size-[15px]"
    ></div>
  {/if}
  {project.name || "+"}
</button>
