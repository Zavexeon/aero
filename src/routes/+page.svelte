<script lang="ts">
  import MousePointer from '$lib/MousePointer.svelte';
  import WindowSpace from '$lib/WindowSpace.svelte';
  import ApplicationWindow from '$lib/ApplicationWindow.svelte';
  import WindowToolbar from '$lib/WindowToolbar.svelte';
</script>

<MousePointer />
<WindowSpace>
  <ApplicationWindow
    title="ScratchPad"
    minHeight="200px"
    minWidth="300px"
    startingCoordinates={{ x: 0, y: 0 }}
    icon="/icons/apps/ScratchPad.svg"
  >
    <WindowToolbar slot="toolbar"></WindowToolbar>
    <textarea slot="workspace"></textarea>
  </ApplicationWindow>
  <ApplicationWindow
    title="ScratchPad"
    minHeight="200px"
    minWidth="300px"
    startingCoordinates={{ x: 0, y: 0 }}
    icon="/icons/apps/ScratchPad.svg"
  >
    <WindowToolbar slot="toolbar"></WindowToolbar>
    <textarea slot="workspace"></textarea>
  </ApplicationWindow>
</WindowSpace>

<style>
  textarea:focus {
    outline: none;
  }
</style>
