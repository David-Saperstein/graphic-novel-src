<script>
    import { getContext } from "svelte";
    import Moveable from "svelte-moveable";
    export let dialogueList;
    export let pageRef;
    export let panelList;
    export let initialTransform;
    export let panelIndex;
    let moveableTextbox;
    let moveablePanel;
    let textbox;
    let panel;
    let isPanelSelected = true;
    let size = getContext("size");
    let gap = getContext("gap");
    let fontSize = getContext("fontSize");
    let selectionArray = getContext("selectionArrayKey");
    function handleTextboxClick(e){
        isPanelSelected = false;
        $selectionArray.fill(false);
        $selectionArray[panelIndex] = true;
        textbox = e.currentTarget;
        setTimeout(()=>{
            moveableTextbox.dragStart(e);
        })
    }

    function handlePanelClick(e){
        isPanelSelected = true;
        $selectionArray.fill(false);
        $selectionArray[panelIndex] = true;
        setTimeout(() => {
            moveablePanel.dragStart(e);
        })
    }


    function getDialogueTransform(dialogue, i){
        if(dialogue.hasOwnProperty("transform")){
            let scale = window.innerHeight / dialogue.transform.windowHeight;
            return `height: ${dialogue.transform.height * scale}px; 
            width: ${dialogue.transform.width * scale}px; 
            transform: translate(${dialogue.transform.position[0] * scale}px, ${dialogue.transform.position[1] * scale}px)`;
        }

        return `transform: translate(0px, ${fontSize * 2 * i}px)`;
    }

    function getPanelTransform(panel, i){
        if(panel.hasOwnProperty('height')){
            let scale = window.innerHeight / panel.windowHeight;
            return `height: ${panel.height * scale}px; 
            width: ${panel.width * scale}px; 
            transform: translate(${panel.position[0] * scale}px, ${panel.position[1] * scale}px)`
        }

        return `height: ${size}px; width: ${(size)}px; transform: translate(${size * (i % 2)}px, ${size * Math.floor(i / 2)}px)`;
    }

    function getDialogueStyle(dialogue){
        switch (dialogue.type){
            case "caption":
                return "caption bg-white";
            case "speech":
                return "speech rounded-full bg-white";
            case "note":
                return "note bg-transparent"
        }
    }



</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<!-- svelte-ignore a11y-click-events-have-key-events -->
<div class={`z-0 absolute panel`} style = {getPanelTransform(initialTransform, panelIndex)} bind:this={panel} on:mousedown={handlePanelClick}>
    <div class="bg-gray-100 absolute flex border-black border-[1px]" style={`width: calc(100% - ${gap * 2}px); height: calc(100% - ${gap * 2}px); top: ${gap}px; left: ${gap}px;`}>
    {#each dialogueList as dialogue, i}
        <div class={`target select-none text-center absolute top-1 left-1 ${getDialogueStyle(dialogue)}`} style={getDialogueTransform(dialogue, i) + `; font-size: ${fontSize}px; font-family: 'Comic Sans MS','Comic Sans','cursive'; line-height: 1.15; padding: ${fontSize/2}px`} on:mousedown|stopPropagation = {handleTextboxClick}>{@html dialogue.text}</div>
    {/each}
    {#if $selectionArray[panelIndex] && !isPanelSelected}
        <Moveable 
        bind:this={moveableTextbox}
        target={textbox}
        draggable={true}
        resizable={true}
        snappable={true}
        bounds = {{"left":0, "top":0, "right":0, "bottom":0, "position":"css"}}
        on:drag = {({detail: e}) => {
            e.target.style.transform = e.transform;
        }}
        on:resize = {({detail: e}) => {
            e.target.style.width = `${e.width}px`;
            e.target.style.height = `${e.height}px`;
            e.target.style.transform = e.drag.transform;

        }}
        />
    {/if}
    </div>
</div>
{#if $selectionArray[panelIndex] && isPanelSelected}

<Moveable 
bind:this={moveablePanel}
target={panel}
draggable = {true}
resizable = {true}
snappable = {true}
elementGuidelines = {panelList}
isDisplaySnapDigit = {false}
padding = {{"left":-gap,"top":-gap,"right":-gap,"bottom":-gap,"position":"css"}}
bounds={{"left": 0,"top": 0,"right": 0,"bottom": 0,"position":"css"}}
snapContainer = {pageRef}
verticalGuidelines = {[{pos: "25%", className: "red"}, {pos: "33.33%", className: "purple"}, {pos: "50%", className: "red"}, {pos: "66.66%", className: "purple"}, {pos: "75%", className: "red"}]}
horizontalGuidelines = {[{pos: "20%", className: "green"}, {pos: "25%", className: "red"}, {pos: "33.33%", className: "purple"}, {pos: "40%", className: "green"}, {pos: "50%", className: "red"}, {pos: "60%", className: "green"}, {pos: "66.66%", className: "purple"}, {pos: "75%", className: "red"}, {pos: "80%", className: "green"}]}
on:drag = {({detail: e}) => {
    e.target.style.transform = e.transform;
}}
on:resize = {({detail: e}) => {
    e.target.style.width = `${e.width}px`;
    e.target.style.height = `${e.height}px`;
    e.target.style.transform = e.drag.transform;
}}
/>
    
{/if}

<style>
    .caption:before{
        position: absolute;
        content: "";
        height: calc(100% + 1px);
        width: calc(100% + 1px);
        z-index: -1;
        top: -0.5px;
        left: -0.5px;
        border: solid black 0.5px
    }

    .speech:before{
        position: absolute;
        content: "";
        height: calc(100% + 1px);
        width: calc(100% + 1px);
        z-index: -1;
        top: -0.5px;
        left: -0.5px;
        border: solid black 0.5px;
        border-radius: 9999px;
    }
</style>