<script>
    import RefPanel from "./RefPanel.svelte";
    export let config;
</script>
{#key config}
{#each config as panel, i}
<RefPanel dialogueList = {panel.dialogueBoxes} initialTransform = {panel.transform}/>
{/each}
{/key}