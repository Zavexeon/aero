<script lang="ts">
  import { onMount } from 'svelte';

  let element: Element;

  onMount(() => {
    const windows = [...element.children] as HTMLElement[];

    const focusWindow = (windowToFocus: HTMLElement) => {
      const currentWindowIndex = windows.indexOf(windowToFocus);

      windows.splice(currentWindowIndex, 1);
      windows.push(windowToFocus);

      for (let index = windows.length - 1; index >= 0; index--)
        windows[index].style.zIndex = String(index);
    };

    windows.forEach((window, index) => {
      window.style.zIndex = String(index);
      window.addEventListener('mousedown', (_) => focusWindow(window));
    });
  });
</script>

<div class="window-space" bind:this={element}>
  <slot />
</div>

<style>
  .window-space {
    width: 100%;
    height: 100%;
    padding: none;
  }
</style>
