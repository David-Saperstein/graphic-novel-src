<script>
// @ts-nocheck
  import {onMount, setContext} from "svelte";
  import {writable} from "svelte/store";
  import Page from "./lib/Page.svelte";
  import RefPage from "./lib/RefPage.svelte";
  import Quill from "quill";
  import html2canvas from "html2canvas";
  let config = [];
  let refConfig = [];
  let screenshotRef;
  let page;
  let inputVal;
  let save;
  let dropDownSelection;
  let editor;
  let relSize = window.innerHeight;
  let size = relSize / 5;
  let gap = relSize / 200;
  let fontSize = relSize / 100;
  let selectionArray = writable([...Array(config.length).fill(false)]);
  let borderMultiplier = 3;
  setContext('selectionArrayKey', selectionArray);
  setContext("gap", gap);
  setContext("size", size);
  setContext("fontSize", fontSize);
  function handlePageClick(e){
    if(e.target.classList.contains("page")){
      noSelect();
    }
  }

  function noSelect() {
    $selectionArray = $selectionArray.fill(false);
  }

  function load(){
    let output = JSON.parse(inputVal);
    if(output){
      config = output;
    }
  }

  function handleUpload(e, isRef){
    const file = e.target.files[0];
    const reader = new FileReader();
    if(isRef){
      reader.onload = function () {
        refConfig = JSON.parse(reader.result);
      }
    }
    else {
      reader.onload = function () {
        config = JSON.parse(reader.result);
      }
    }
    reader.readAsText(file);
  }

  function screenshot(){
    html2canvas(screenshotRef).then((canvas)=>{
      var a = document.createElement("a");
      a.href = canvas.toDataURL();
      a.download = "screenshot.png"
      a.click();
    })
  }


  onMount(()=>{
    let quill = new Quill(editor, {theme: "bubble", placeholder:"Write notes here..."});
  })

</script>
<style>
  @import "https://cdn.jsdelivr.net/npm/quill@2/dist/quill.bubble.css";
</style>
<div class="flex justify-between">
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div class="w-full mx-2" on:mousedown={noSelect}>
    <h1 class="text-2xl text-center font-bold">Set Options Below:</h1>
    <label for="page2" class="text-xl">Reference Page: </label>
    <select name="" id="page2" bind:value={dropDownSelection}>
      <option value="none">NONE</option>
      <option value="left">LEFT</option>
      <option value="right">RIGHT</option>
    </select>
    {#if dropDownSelection != "none"}
    <div>
      <label for="ref-file-input" class="text-xl">Upload ref: </label>
      <input type="file" id="ref-file-input" accept="application/json" on:change={(e) => handleUpload(e, true)} on:click={(e) => e.target.value = null}>
    </div>
    {/if}
    <div>
      <label for="config" class="text-xl">Input: </label>
      <input type="text" name="" id="config" class="border-black border-2" bind:value={inputVal}>
      <button class="border-black border-2 p-2 rounded-lg" on:click = {load}>Load</button>
    </div>
    <div>
      <label for="file-input" class="text-xl">Upload: </label>
      <input type="file" id="file-input" accept="application/json" on:change={(e) => handleUpload(e, false)} on:click={(e) => e.target.value = null}>
    </div>
    <div><button on:click={save} class="border-black border-2 p-2 rounded-lg">Save</button></div>
    <div><button on:click={screenshot} class="border-black border-2 p-2 rounded-lg">Screenshot</button></div>
    <div class="border-black border-2" bind:this={editor} style="height:30vh; width:30vh"></div>
  </div>
    
  {#if dropDownSelection == "left"}
    <!-- svelte-ignore a11y-no-static-element-interactions -->
    <div class="aspect-[2/3] ring-2 ring-black bg-white" style={`height: ${relSize-(relSize/20)}px; margin: ${relSize/40}px 0px`} on:mousedown={noSelect}>
      <div class="relative" style={`width: calc(100% - ${gap * 2 * borderMultiplier}px); height: calc(100% - ${gap * 2 * borderMultiplier}px); top: ${gap * borderMultiplier}px; left: ${gap * borderMultiplier}px;`}>
        <RefPage config={refConfig}/>
      </div>
    </div>
  {/if}
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div class="page aspect-[2/3] ring-2 ring-black bg-white" style={`height: ${relSize-(relSize/20)}px; margin: ${relSize/40}px 0px`} bind:this={screenshotRef} on:mousedown={handlePageClick}>
    <div class="page relative" style={`width: calc(100% - ${gap * 2 * borderMultiplier}px); height: calc(100% - ${gap * 2 * borderMultiplier}px); top: ${gap * borderMultiplier}px; left: ${gap * borderMultiplier}px;`} bind:this={page}>
      <Page config={config} pageRef = {page} bind:save = {save}/>
    </div>
  </div>
  {#if dropDownSelection === "right"}
    <!-- svelte-ignore a11y-no-static-element-interactions -->
    <div class="aspect-[2/3] ring-2 ring-black bg-white" style={`height: ${relSize-(relSize/20)}px; margin: ${relSize/40}px 0px`} on:mousedown={noSelect}>
      <div class="relative" style={`width: calc(100% - ${gap * 2 * borderMultiplier}px); height: calc(100% - ${gap * 2 * borderMultiplier}px); top: ${gap * borderMultiplier}px; left: ${gap * borderMultiplier}px;`}>
        <RefPage config={refConfig}/>
      </div>
    </div>
  {/if}
</div>