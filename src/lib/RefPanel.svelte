<script>
    import { getContext } from "svelte";
    export let dialogueList;
    export let initialTransform;
    let gap = getContext("gap");
    let fontSize = getContext("fontSize");


    function getDialogueTransform(dialogue){
        let scale = window.innerHeight / dialogue.transform.windowHeight;
        return `height: ${dialogue.transform.height * scale}px; 
        width: ${dialogue.transform.width * scale}px; 
        transform: translate(${dialogue.transform.position[0] * scale}px, ${dialogue.transform.position[1] * scale}px)`;
    }

    function getPanelTransform(panel){
        let scale = window.innerHeight / panel.windowHeight;
        return `height: ${panel.height * scale}px; 
        width: ${panel.width * scale}px; 
        transform: translate(${panel.position[0] * scale}px, ${panel.position[1] * scale}px)`
    }

    function getDialogueStyle(dialogue){
        switch (dialogue.type){
            case "caption":
                return "caption bg-white ring-black ring-[0.5px]";
            case "speech":
                return "speech rounded-full bg-white ring-black ring-[0.5px]";
            case "note":
                return "note bg-transparent"
        }
    }



</script>
<div class={`z-0 absolute panel`} style = {getPanelTransform(initialTransform)}>
    <div class="bg-gray-100 absolute flex border-black border-[1px]" style={`width: calc(100% - ${gap * 2}px); height: calc(100% - ${gap * 2}px); top: ${gap}px; left: ${gap}px;`}>
    {#each dialogueList as dialogue, i}
        <div class={`target select-none z-10 text-center absolute top-1 left-1 ${getDialogueStyle(dialogue)}`} style={getDialogueTransform(dialogue) + `; font-size: ${fontSize}px; font-family: 'Comic Sans MS','Comic Sans','cursive'; line-height: 1.15; padding: ${fontSize/2}px`}>{@html dialogue.text}</div>
    {/each}
    </div>
</div>