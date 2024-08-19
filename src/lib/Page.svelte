<script>
    import { getContext, setContext} from "svelte";
    import {writable} from "svelte/store";
    import Panel from "./Panel.svelte";
    import PanelUpdater from "./PanelUpdater.svelte";
    export let config;
    export let pageRef;
    function parseTransform(transform){
        let arr = transform.split("px, ");
        return [parseFloat(arr[0].slice(10)), parseFloat(arr[1].slice(0,-3))]
    }

    function parseType(classList){
        if(classList.contains("caption")){
            return "caption"
        }
        else if(classList.contains("speech")){
            return "speech"
        }
        return "note";
    }
    export function save(){
        const result = [];
        for(const panel of panelList){
            let obj = {}
            obj.transform = {
                        height: panel.clientHeight,
                        width: panel.clientWidth,
                        position: parseTransform(panel.style.transform),
                        windowHeight: window.innerHeight,
                    };
            obj.dialogueBoxes = [];
            for(const dbox of panel.getElementsByClassName("target")){
                obj.dialogueBoxes.push({
                    text: dbox.innerHTML,
                    type: parseType(dbox.classList),
                    transform: {
                        height: dbox.getBoundingClientRect().height,
                        width: dbox.getBoundingClientRect().width,
                        position: parseTransform(dbox.style.transform),
                        windowHeight: window.innerHeight,
                    }
                });
            }
            result.push(obj);
        }
        download(JSON.stringify(result), 'save.json', 'application/json');
    }
    function download(content, fileName, contentType) {
        var a = document.createElement("a");
        var file = new Blob([content], {type: contentType});
        a.href = URL.createObjectURL(file);
        a.download = fileName;
        a.click();
    }
    let panelList = [];
    let size = getContext("size");
    let gap = getContext("gap");
    let selectionArray = writable([...Array(config.length).fill(false)]);
    setContext('selectionArray', selectionArray);
    let updatePanelList = () => {
        panelList = Array.from(document.getElementsByClassName("panel"));
    }

</script>
{#key config}
    
{#each config as panel, i}
<Panel dialogueList = {panel.dialogueBoxes} pageRef = {pageRef} bind:panelList={panelList} 
initialTransform={panel.transform || {}}
panelIndex = {i}
/>
{/each}
<PanelUpdater updateFunc = {updatePanelList} />
{/key}