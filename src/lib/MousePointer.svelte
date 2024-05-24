<script lang="ts">
  import pointer from '$lib/mouse_icons/left_ptr.webp?enhanced';
  import verticalResize from '$lib/mouse_icons/size_ver.webp?enhanced';
  import horizontalResize from '$lib/mouse_icons/size_hor.webp?enhanced';
  import forwardDiagonalResize from '$lib/mouse_icons/size_fdiag.webp?enhanced';
  import backwardDiagonalResize from '$lib/mouse_icons/size_bdiag.webp?enhanced';
  import none from '$lib/mouse_icons/none.webp?enhanced';
  import move from '$lib/mouse_icons/size_all.webp?enhanced';

  const cursors: {
    [key: string]: {
      file: string;
      offset?: [number, number];
      element?: HTMLElement;
    };
  } = {
    'n-resize': { file: verticalResize, offset: [-5, -10] },
    'e-resize': { file: horizontalResize, offset: [-10, -3] },
    's-resize': { file: verticalResize, offset: [-5, -10] },
    'w-resize': { file: horizontalResize, offset: [-10, -3] },
    'nw-resize': { file: forwardDiagonalResize, offset: [-5, -5] },
    'ne-resize': { file: backwardDiagonalResize, offset: [-5, -5] },
    'se-resize': { file: forwardDiagonalResize, offset: [-5, -5] },
    'sw-resize': { file: backwardDiagonalResize, offset: [-5, -5] },
    none: { file: none },
    move: { file: move, offset: [-12, -12] },
    default: { file: pointer },
  };

  let cursorElements: { key: string; element: HTMLElement }[] = [];

  const cursorCreated = (cursorElement: HTMLElement, key: string) => {
    cursorElements.push({ key: key, element: cursorElement });
  };

  let offsetX = 0;
  let offsetY = 0;
  let isMouseDown = false;

  const updateMouse = (event: MouseEvent) => {
    const target = event.target as HTMLElement;
    const cursorStyle: string = target.dataset.cursorStyle || 'default';

    if (!isMouseDown) {
      cursorElements.forEach((cursor) => {
        cursor.element.style.display =
          cursor.key === cursorStyle ? 'block' : 'none';
      });
    }

    const offset = cursors[cursorStyle].offset || [0, 0];

    offsetY = event.clientY + offset[1];
    offsetX = event.clientX + offset[0];
  };
</script>

<svelte:window
  on:mousemove={updateMouse}
  on:mousedown={(_) => (isMouseDown = true)}
  on:mouseup={(_) => (isMouseDown = false)}
/>
<!--svelte-ignore avoid-mouse-events-on-document-->
<div
  class="mouse"
  style:transform={`translate3d(${offsetX}px, ${offsetY}px, 0px)`}
>
  {#each Object.entries(cursors) as [key, cursor]}
    <enhanced:img
      src={cursor.file}
      alt={key}
      style:display={key === 'default' ? 'block' : 'none'}
      use:cursorCreated={key}
    />
  {/each}
</div>

<style lang="scss">
  .mouse {
    user-select: none;
    pointer-events: none;
    z-index: 9999;
    position: absolute;

    & > * {
      width: 20px;
      height: auto;
    }
  }

  :global(*) {
    cursor: none !important;
  }
</style>
