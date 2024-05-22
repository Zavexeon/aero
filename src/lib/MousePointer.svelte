<script lang="ts">
  import { onMount } from 'svelte';

  let top = 0;
  let left = 0;
  let display = 'block';
  let isMouseDown = false;

  let mouseElement: HTMLElement;

  const cursors: {
    [key: string]: { file: string; offset?: [number, number] };
  } = {
    'n-resize': { file: '/mouse/size_ver.webp', offset: [-5, -10] },
    'e-resize': { file: '/mouse/size_hor.webp', offset: [-10, -3] },
    's-resize': { file: '/mouse/size_ver.webp', offset: [-5, -10] },
    'w-resize': { file: '/mouse/size_hor.webp', offset: [-10, -3] },
    'nw-resize': { file: '/mouse/size_fdiag.webp', offset: [-5, -5] },
    'ne-resize': { file: '/mouse/size_bdiag.webp', offset: [-5, -5] },
    'se-resize': { file: '/mouse/size_fdiag.webp', offset: [-5, -5] },
    'sw-resize': { file: '/mouse/size_bdiag.webp', offset: [-5, -5] },
    none: { file: '/mouse/none.webp' },
    move: { file: '/mouse/size_all.webp', offset: [-12, -12] },
    default: { file: '/mouse/left_ptr.webp' },
  };

  let src = cursors.default.file;

  const updatePosition = (event: MouseEvent) => {
    const target = event.target as HTMLElement;
    const cursorStyle: string = target.dataset.cursorStyle || 'default';

    if (!isMouseDown) {
      src = Object.hasOwn(cursors, cursorStyle)
        ? cursors[cursorStyle].file
        : cursors.default.file;
    }

    const offset = cursors[cursorStyle].offset || [0, 0];

    top = event.clientY + offset[1];
    left = event.clientX + offset[0];
  };

  const hideMouse = () => (display = 'none');
  const showMouse = () => (display = 'block');
</script>

<svelte:window
  on:mousemove={updatePosition}
  on:mousedown={(_) => (isMouseDown = true)}
  on:mouseup={(_) => (isMouseDown = false)}
/>
<!--svelte-ignore avoid-mouse-events-on-document-->
<svelte:document on:mouseleave={hideMouse} on:mouseenter={showMouse} />
<img
  draggable="false"
  class="mouse"
  {src}
  style:top={top + 'px'}
  style:left={left + 'px'}
  bind:this={mouseElement}
  style:display
  alt="mouse"
/>

<style>
  .mouse {
    /* transform: translateZ(0); */
    position: absolute;
    z-index: 1000;
    width: 27px;
    pointer-events: none;
    user-select: none;
  }

  :global(*) {
    cursor: none !important;
  }
</style>
