<script>
  import { Canvas } from '@threlte/core'
  import Scene from './Scene.svelte'

  let freqA = -5
  let ampA = 1
  let phaseA = 0

  let showSum = false
  let showB = false
  let conjugate = false

  let freqB = 5
  let ampB = 1
  let phaseB = 0

  $: if(conjugate) {
    freqB = -freqA
    ampB = ampA
    phaseB = -phaseA
  }
</script>

<style>
  .controls {
    position: absolute;
    top: 1em;
    left: 1em;
    color: #fff;
  }

  dl {
    display: grid;
    padding: 0;
    margin: 0;
    grid-auto-flow: column;
    grid-template-rows: 1fr 1fr;
    grid-auto-columns: auto;
    gap: 0 1em;
  }

  dt, dd {
    margin: 0;
    padding: 0;
  }

  input {
    margin: 0;
  }
</style>

<div class="controls">

  <fieldset>
    <legend>Oscillation A</legend>

    <dl>
      <dt><label for="freqA">Frequency</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="-30" max="30" bind:value={freqA} id="freqA" /></dd>
      <dt><label for="ampA">Amplitude</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="0" max="3" step="0.2" bind:value={ampA} id="ampA" /></dd>
      <dt><label for="phaseA">Phase</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="-1" max="1" step="0.005" bind:value={phaseA} id="phaseA" /></dd>
    </dl>
  </fieldset>
  <fieldset>
    <legend>Oscillation B (
    <label>
      <input type="checkbox" bind:checked={showB}> Enable
    </label>)</legend>


    {#if showB}
    <label>
      <input type="checkbox" bind:checked={conjugate}> Conjugate of A
    </label>
    
    <dl>
      <dt><label for="freqB">Frequency</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="-30" max="30" bind:value={freqB} id="freqB" /></dd>
      <dt><label for="ampB">Amplitude</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="0" max="3" step="0.2" bind:value={ampB} id="ampB" /></dd>
      <dt><label for="phaseB">Phase</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="-1" max="1" step="0.005" bind:value={phaseB} id="phaseB" /></dd>
    </dl>
    {/if}
  </fieldset>

  {#if showB}
  <fieldset>
    <legend>Sum</legend>


    <label>
      <input type="checkbox" bind:checked={showSum}> Show Sum
    </label>
    
  </fieldset>
  {/if}
</div>

<Canvas>
  <Scene {freqA} {ampA} {phaseA} {freqB} {ampB} {phaseB} {showB} {showSum} />
</Canvas>
