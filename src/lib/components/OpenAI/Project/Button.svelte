<script>
  let { project, onClick, selected } = $props();
</script>

<div
  class={`size-20 ${selected ? "border-white border-2" : ""} rounded-lg flex items-center justify-center font-semibold cursor-pointer transition-transform hover:scale-105 select-none`}
  style={`background-color: ${project.color || "#444"}; color:${project.txtColor || "#FFF"}`}
  on:click={() => onClick?.(project)}
>
  {project.name || "+"}
</div>
