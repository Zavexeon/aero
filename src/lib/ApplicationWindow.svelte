<script lang="ts">
  import { onMount } from 'svelte';

  export let isResizable = true;
  export let minWidth = '200px';
  export let minHeight = '150px';
  export let width = minWidth;
  export let height = minHeight;
  export let title = 'Window';
  export let icon: string | undefined;

  export let startingCoordinates = { x: 0, y: 0 };

  let offsetY = startingCoordinates.y;
  let offsetX = startingCoordinates.x;
  // let windowSpaceDimensionPixels;

  let windowElement: HTMLElement;

  let heightPixels: number;
  let widthPixels: number;

  const updateDimensionsPixels = () => {
    heightPixels = windowElement.offsetHeight;
    widthPixels = windowElement.offsetWidth;
  };

  onMount(() => {
    updateDimensionsPixels();
  });

  let isMouseDown = {
    resize: false,
    move: false,
  };

  let resizeAxis: string;
  let animationFrameID: number;

  const mouseDownMove = () => (isMouseDown.move = true);
  const mouseDownResize = (axis: string) => {
    isMouseDown.resize = true;
    resizeAxis = axis;
  };
  const mouseUp = () => {
    isMouseDown.resize = false;
    isMouseDown.move = false;

    cancelAnimationFrame(animationFrameID);
  };

  const updateWindow = (event: MouseEvent) => {
    if (animationFrameID) cancelAnimationFrame(animationFrameID);
    if (isMouseDown.move) {
      const moveWindow = () => {
        offsetX += event.movementX;
        offsetY += event.movementY;
      };
      animationFrameID = requestAnimationFrame(moveWindow);
    } else if (isMouseDown.resize) {
      const setWidthNeg = () => {
        const newWidth = widthPixels - event.movementX;
        if (newWidth >= parseInt(minWidth)) {
          offsetX += event.movementX;
          widthPixels = newWidth;
        }
      };

      const setHeightNeg = () => {
        const newHeight = heightPixels - event.movementY;
        if (newHeight >= parseInt(minHeight)) {
          offsetY += event.movementY;
          heightPixels = newHeight;
        }
      };

      switch (resizeAxis) {
        case 'n':
          setHeightNeg();
          break;
        case 'e':
          widthPixels += event.movementX;
          break;
        case 's':
          heightPixels += event.movementY;
          break;
        case 'w':
          setWidthNeg();
          break;
        case 'ne':
          setHeightNeg();
          widthPixels += event.movementX;
          break;
        case 'nw':
          setHeightNeg();
          setWidthNeg();
          break;
        case 'sw':
          heightPixels += event.movementY;
          setWidthNeg();
          break;
        case 'se':
          heightPixels += event.movementY;
          widthPixels += event.movementX;
          break;
      }

      height = heightPixels + 'px';
      width = widthPixels + 'px';
    }
  };

  const minimizeWindow = () => {};
  const maximizeWindow = () => {};
  const closeWindow = () => windowElement.remove();
</script>

<svelte:window
  on:mouseup={mouseUp}
  on:mouseleave={mouseUp}
  on:mousemove={updateWindow}
/>
<div
  class="application-window"
  style:width
  style:height
  style:min-width={minWidth}
  style:min-height={minHeight}
  style:transform={`translate3d(${offsetX}px, ${offsetY}px, 0px)`}
  bind:this={windowElement}
>
  {#if isResizable}
    <div class="resizable-border">
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="offsetY offsetX"
        data-cursor-style="nw-resize"
        on:mousedown={(_) => mouseDownResize('nw')}
      ></div>
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="offsetY"
        data-cursor-style="n-resize"
        on:mousedown={(_) => mouseDownResize('n')}
      ></div>
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="offsetY right"
        data-cursor-style="ne-resize"
        on:mousedown={(_) => mouseDownResize('ne')}
      ></div>
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="offsetX"
        data-cursor-style="w-resize"
        on:mousedown={(_) => mouseDownResize('w')}
      ></div>
      <div class="center"></div>
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="right"
        data-cursor-style="e-resize"
        on:mousedown={(_) => mouseDownResize('e')}
      ></div>
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="bottom offsetX"
        data-cursor-style="sw-resize"
        on:mousedown={(_) => mouseDownResize('sw')}
      ></div>
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="bottom"
        data-cursor-style="s-resize"
        on:mousedown={(_) => mouseDownResize('s')}
      ></div>
      <!--svelte-ignore a11y-no-static-element-interactions-->
      <div
        class="bottom right"
        data-cursor-style="se-resize"
        on:mousedown={(_) => mouseDownResize('se')}
      ></div>
    </div>
  {/if}
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div class="title-bar" on:mousedown={mouseDownMove} data-cursor-style="move">
    <div class="icon-container" data-cursor-style="move">
      {#if icon}
        <img class="icon" src={icon} alt="window icon" />
      {/if}
    </div>
    <p class="title" data-cursor-style="move">{title}</p>
    <div class="actions">
      <button class="minimize" on:click={minimizeWindow}></button>
      <button class="maximize" on:click={maximizeWindow}></button>
      <button class="close" on:click={closeWindow}></button>
    </div>
  </div>
  <slot name="toolbar"></slot>
  <div class="workspace-border">
    <div class="workspace">
      <slot name="workspace" />
    </div>
  </div>
</div>

<style lang="scss">
  @use 'sass:map';

  $border-offsetY-exterior: 10px;
  $border-bottom-exterior: 2px;
  $window-border-width: 1.2px;
  $window-border-radius: 10px 10px 2px 2px;

  $action-colors: (
    minimize: #ffd025,
    maximize: #5bbcfc,
    close: #ff4f4f,
  );

  .application-window {
    will-change: transform;
    display: flex;
    flex-direction: column;
    border-radius: $window-border-radius;
    overflow: hidden;
    box-shadow: 0px 0px 8px #000000;
    backface-visibility: hidden;
    position: fixed;
  }

  .application-window > .resizable-border {
    height: 100%;
    width: 100%;
    display: grid;
    grid-template-columns: 10px auto 10px;
    grid-template-rows: 10px auto 10px;
    position: absolute;
    pointer-events: none;
    z-index: 1; // make sure this is always on top :)

    & > *:not(.center) {
      pointer-events: all;
    }
  }

  .application-window > .title-bar {
    $container-width: 60px;

    background: linear-gradient(#9d9d9d, #ffffff, #afaeae, #9d9d9d);
    border-width: $window-border-width;
    border-color: #ffffff #858585 #858585 #ffffff;
    border-style: solid;
    height: 35px;
    user-select: none;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    padding: 0px 5px;

    & > .icon-container {
      width: $container-width;

      & > .icon {
        height: 25px;
        width: 25px;
        filter: drop-shadow(1px 1px 3px);
      }
    }

    & > .title {
      color: #4f4f4f;
      font-size: 14px;
      font-family: monospace;
      font-weight: bold;
      margin: 0px auto;
    }

    & > .actions {
      width: $container-width;
      display: flex;
      justify-content: space-between;

      & > * {
        height: 18px;
        width: 18px;
        border-width: 1px;
        border-style: solid;
        border-color: #6d6d6d #f5f5f5 #f5f5f5 #6d6d6d;
        border-radius: 100%;
        box-shadow: inset -2px -2px 3px #0000009b;
        filter: brightness(95%);
      }

      & > *:hover {
        filter: brightness(105%);
        box-shadow: inset -1px -1px 2px #0000009b;
      }

      & > .minimize {
        background: radial-gradient(
          circle at 30% 30%,
          #ffffff,
          map.get($action-colors, 'minimize'),
          map.get($action-colors, 'minimize')
        );
      }

      & > .maximize {
        background: radial-gradient(
          circle at 30% 30%,
          #ffffff,
          map.get($action-colors, 'maximize'),
          map.get($action-colors, 'maximize')
        );
      }

      & > .close {
        background: radial-gradient(
          circle at 30% 30%,
          #ffffff,
          map.get($action-colors, 'close'),
          map.get($action-colors, 'close')
        );
      }
    }
  }

  .application-window > .workspace-border {
    $border-thickness: 8px;
    background: linear-gradient(#ffffff5b, #afaeae5b, #3a3a3a5b);
    backdrop-filter: blur(2px);
    flex-grow: 1;
    padding: 0px $border-thickness $border-thickness $border-thickness;
    display: flex;
    overflow: hidden;
    border-width: $window-border-width;
    border-style: none solid solid solid;
    border-color: transparent #7b7b7b81 #7b7b7b81 #ffffff81;

    & > .workspace {
      flex-grow: 1;
      overflow: auto;
      display: flex;
      flex-direction: column;
      box-shadow: 0px 0px 7px;

      & > :global(*) {
        flex-grow: 1;
        overflow: hidden;
        box-shadow: inset 0px 0px 5px;
      }
    }
  }
</style>
