<script lang="ts">
  import { onMount } from "svelte";

  import ProjectDrawer from "$lib/components/OpenAI/Project/Drawer.svelte";
  import ProjectView from "$lib/components/ProjectView.svelte";
  import { slide } from "svelte/transition";

  let apps = $state([]);
  let sortType = $state(1);
  let sortReversed = $state(0);
  let sortOptions = $state(["Alphabetical", "Date Created", "Times Opened"]);

  let projSelected = $state(null);
  let projects = $state([]);

  let curApp = $state(null);
  let curPage = $state(0);

  let appContainer = $state(null);

  let bSize = $state(60);

  let scrollLocked = $state(true);
  let projNum = $state(80);
  let pageNum = $state(4);
  let maxProjNumX = $state(3);
  let maxProjNumY = $state(5);

  function setCurPage(pageOffset) {
    console.log(curPage, pageNum);
    if (
      ((curPage <= 0 && pageOffset < 0) ||
        (curPage >= pageNum - 1 && pageOffset > 0)) &&
      scrollLocked
    ) {
      return;
    }
    curPage = curPage + pageOffset;
    curPage = Math.abs(curPage % pageNum);

    scrollPage();
  }

  function scrollPage() {
    // let gridH = appContainer.parentNode.offsetHeight * curPage;
    // let gridW = appContainer.parentNode.offsetWidth;

    let gridH = maxProjNumY * bSize;
    gridH = gridH * curPage;
    // gridH = gridH + curPage * 2;
    gridH = gridH + 16 * curPage;
    let gridW = maxProjNumX * bSize + (8 + maxProjNumX) * maxProjNumX;

    appContainer.scrollTo({ top: gridH, behavior: "smooth" });
  }

  function setPaging() {
    let height = appContainer.clientHeight;
    let width = appContainer.clientWidth;

    // console.log(~~(height / 60) / 10, width);
  }

  $effect(() => {
    console.log(appContainer?.parentNode.offsetHeight);
  });

  function magnitudeAndDirection(point1, point2) {
    const dx = point2.x - point1.x;
    const dy = point2.y - point1.y;
    const magnitude = Math.sqrt(dx * dx + dy * dy);

    const epsilon = 1e-10; // Small range for floating-point comparison
    let direction;

    console.log(Math.abs(dy) < epsilon);

    if (Math.abs(dy) < epsilon) {
      direction = "horizontal";
    } else if (Math.abs(dx) < epsilon) {
      direction = "vertical";
    } else {
      direction = "neither";
    }

    return {
      magnitude: magnitude,
      direction: direction,
    };
  }

  onMount(() => {
    // let gridH = appContainer.parentNode.offsetHeight - 100;
    //let gridW = appContainer.parentNode.offsetWidth - 100;

    let py = null;
    let px = null;

    setGridSize();

    appContainer.addEventListener("pointerdown", (e) => {
      e.preventDefault();
      if (projSelected) return;

      py = e.clientY;
      px = e.clientX;
    });

    /*
    appContainer.addEventListener("pointermove", (e) => {
      appContainer.scrollTo({ top: py - e.clientY });
    });
    */

    appContainer.addEventListener("pointerup", (e) => {
      e.preventDefault();
      if (projSelected) return;

      /*
      console.log(
        magnitudeAndDirection({ x: px, y: py }, { x: e.clientX, y: e.clientY }),
      );
      */

      if (e.clientY < py) {
        setCurPage(1);
        console.log("swipe up");
      } else {
        setCurPage(-1);
        console.log("swipe down");
      }

      py = null;
    });

    window.addEventListener("resize", (e) => {
      setGridSize();
      console.log(e.offset);
    });

    let ticking = false;

    setPaging();
  });

  function setGridSize() {
    // maxProjNumX = ~~(appContainer.parentNode.clientWidth / bSize);
    maxProjNumX = ~~((appContainer.parentNode.offsetWidth - 40) / bSize);
    maxProjNumY = ~~((appContainer.parentNode.offsetHeight - 40) / bSize);

    let gridH = maxProjNumY * bSize + maxProjNumY * maxProjNumY;
    let gridW = maxProjNumX * bSize + (8 + maxProjNumX) * maxProjNumX;

    appContainer.style.width = `${gridW}px`;
    // appContainer.style.height = `${gridH}px`;
  }

  function setCurApp(_idx) {
    curApp = _idx;
  }
</script>

<!-- Project Drawer -->

{#if curApp == null}
  <div class="w-full h-full">
    <div class="flex flex-col bg-red-400 h-full">
      <div class="shrink p-2 flex items-center">
        <div class="flex-1 flex">
          <div class="rounded-md flex-1">
            <div class="flex items-center mb-2 flex-1 flex-row gap-2">
              <label class="block text-sm font-medium flex-1">Sort</label>
              <select
                bind:value={sortType}
                class="bg-white w-full p-2 border rounded"
              >
                {#each sortOptions as opt, idx}
                  <option value={idx} selected={idx == sortType}>{opt}</option>
                {/each}
              </select>
              <div class="flex flex-1 items-center gap-2">
                <input
                  id="reversed"
                  type="checkbox"
                  bind:checked={sortReversed}
                />
                <label for="reversed" class="text-sm">Reverse</label>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="flex flex-1 bg-green-300 p-2">
        <div class="relative flex-1 flex justify-center">
          <div
            class="items-center rounded-md relative flex justify-center flex-1 bg-red-400"
          >
            <div
              bind:this={appContainer}
              class={`overflow-hidden bg-green-300 absolute grid grid-cols-[repeat(auto-fill,minmax(${bSize}px,1fr))] gap-4 rounded-md h-[130px]`}
            >
              {#each Array(projNum) as app, idx}
                <button
                  transition:slide
                  onclick={(e) => {
                    e.preventDefault();
                    if (projSelected == idx) {
                      projSelected = null;
                      return;
                    }
                    // projSelected = idx;
                  }}
                  class={`${projSelected == idx ? "border-4 border-neutral-400" : ""} cursor-pointer bg-slate-100 size-[${bSize}px] rounded-md`}
                  >{idx}</button
                >
              {/each}
            </div>
          </div>

          {#if !projSelected}
            <div class="w-full bottom-0 absolute flex-1 flex justify-center">
              <div
                class="bg-blue-400 flex p-1 rounded-md justify-center gap-2 absolute bottom-[8px]"
              >
                <button
                  onclick={() => {
                    setCurPage(-1);
                  }}
                  class="cursor-pointer h-[20px] w-[30px] rounded-full bottom-[-1px] absolute left-[-40px] bg-blue-400 flex justify-center items-center text-white"
                  >-</button
                >
                {#each Array(pageNum) as dot, idx}
                  <button
                    onclick={() => {
                      curPage = idx;
                      scrollPage();
                    }}
                    class={`cursor-pointer ${idx == curPage ? "border-2 border-white" : ""} bg-red-400 rounded-full size-[10px]`}
                  ></button>
                {/each}
                <button
                  onclick={() => {
                    setCurPage(1);
                  }}
                  class="cursor-pointer h-[20px] w-[30px] rounded-full bottom-[-1px] absolute right-[-40px] bg-blue-400 flex justify-center items-center text-white"
                  >+</button
                >
              </div>
            </div>
          {/if}
        </div>
      </div>

      <div class="flex bg-blue-400 p-2">
        <div class="bg-red-400 rounded-md flex items-center h-[80px] w-full">
          <div class=" p-2 border-r-2 shrink border-dashed">
            <button class={`bg-slate-300 rounded-md size-[60px]`}>+</button>
          </div>
          <div class="flex flex-1 gap-2 justify-between p-2">
            {#each projects.filter((e) => {
              return e.pinned;
            }) as pin, idx}
              <button
                class={`select-none w-full bg-slate-300 rounded-md size-[60px]`}
                >{idx}</button
              >
            {/each}
          </div>
        </div>
      </div>
      {#if projSelected !== null}
        <div
          transition:slide={{ y: 100 }}
          class="bg-yellow-300 w-full flex gap-1"
        >
          <div class="flex flex-col flex-1">
            <div
              class="flex-1 bg-red-400 font-bold p-2 justify-center bg-blue-300"
            >
              Project {projSelected}
            </div>

            <div class="flex-1 flex gap-1 p-2">
              {#each Array("Open", "Edit", "Delete") as opt}
                <button class="cursor-pointer flex-1 bg-slate-300 rounded-md"
                  >{opt}</button
                >
              {/each}
            </div>
          </div>
        </div>
      {/if}
    </div>
    <!-- <ProjectDrawer {setCurApp} /> -->
  </div>
{:else}
  <ProjectView />
{/if}
