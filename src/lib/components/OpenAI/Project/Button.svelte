<script>
  import { fade } from "svelte/transition";
  let { project, onClick, selected } = $props();
</script>

<div
  class={`size-20 ${selected ? "border-white border-2" : ""} rounded-lg relative flex items-center justify-center font-semibold cursor-pointer transition-transform hover:scale-105 select-none`}
  style={`background-color: ${project.color || "#444"}; color:${project.txtColor || "#FFF"}`}
  on:click={() => onClick?.(project)}
>
  {#if project.pin}
    <div
      transition:fade
      class="border-2 border-black/40 bg-red-400 rounded-full absolute right-1 top-1 size-[15px]"
    ></div>
  {/if}
  {project.name || "+"}
</div>
