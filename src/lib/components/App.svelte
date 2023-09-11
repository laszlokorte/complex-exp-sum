<script>
  import { Canvas } from '@threlte/core'
  import Scene from './Scene.svelte'

  let freqA = 5
  let ampA = 1
  let phaseA = 0

  let showSum = false
  let hideParts = false
  let showB = false
  let conjugate = false

  let freqB = -5
  let ampB = 1
  let phaseB = 0

  $: if(conjugate) {
    freqB = -freqA
    ampB = ampA
    phaseB = -phaseA
  }
</script>

<style>
  :global(body) {
    font-family: sans-serif;
  }

  .controls {
    position: absolute;
    top: 1em;
    left: 1em;
    color: #fff;
    width: 100%;
    max-width: 40em;
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

  legend {
    display: inline-block;
    padding: 2px 5px;
    background: #111;
    color: #fff;
    margin: 0;
  }

  fieldset {
    background: #223c;
    border: #112f;
    margin: 0 0 1em 0;
  }

  .checkbox-list {
    display: flex;
    gap: 2em;
  }

  summary {
    cursor: pointer;
  }

  summary {
    display: inline-block;
    padding: 2px 5px;
    background: #111;
    color: #fff;
    position: relative;
    top: -0.5em;
    left: -0.25em;
    text-decoration: underline;
  }

  summary::after {
    content: '...';
  }

  details {
    background: #223c;
    border: #112f;
    margin: 0 0 1em 0;
    padding: 0 1em 1em;
  }

  h2 {
    margin: 0;
  }
  footer {
    text-align: center;
    font-size: small;
  }

  footer a {
    color: #fff;
  }

</style>

<div class="controls">

  <h2>Sum of two complex exponentials</h2>

  <fieldset>
    <legend>Oscillation A</legend>

    <dl>
      <dt><label for="freqA">Frequency: {freqA}Hz</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="-30" max="30" bind:value={freqA} id="freqA" /></dd>
      <dt><label for="ampA">Amplitude: {ampA}</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="0" max="3" step="0.2" bind:value={ampA} id="ampA" /></dd>
      <dt><label for="phaseA">Phase: {phaseA}</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="-1" max="1" step="0.005" bind:value={phaseA} id="phaseA" /></dd>
    </dl>
  </fieldset>
  <fieldset>
    <legend>Oscillation B (
    <label>
      <input type="checkbox" bind:checked={showB} style:accent-color="#fe3d00"> Enable
    </label>)</legend>


    {#if showB}
    <div class="checkbox-list">
      <label>
        <input type="checkbox" bind:checked={conjugate} style:accent-color="#fe3d00"> Conjugate of A
      </label>
    </div>
    
    <dl>
      <dt><label for="freqB">Frequency: {freqB} Hz</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="-30" max="30" bind:value={freqB} id="freqB" /></dd>
      <dt><label for="ampB">Amplitude: {ampB}</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="0" max="3" step="0.2" bind:value={ampB} id="ampB" /></dd>
      <dt><label for="phaseB">Phase: {phaseB}</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="-1" max="1" step="0.005" bind:value={phaseB} id="phaseB" /></dd>
    </dl>
    {/if}
  </fieldset>

  {#if showB}
  <fieldset>
    <legend>Sum</legend>


   <div class="checkbox-list">
      <label>
        <input style:accent-color="#fff"  type="checkbox" bind:checked={showSum}> Show Sum
      </label>

      {#if showSum}

      <label>
        <input style:accent-color="#fff"  type="checkbox" bind:checked={hideParts}> Hide individuals
      </label>
      {/if}
   </div>
    
  </fieldset>
  {/if}

  <details>
    <summary>Explanation</summary>
    
    <p>
      Explore how two complex waves/oscillations interfere de- or constructively. Use the controls above to controls the frequency, amplitude and phase of the waves. Enable either both or only one of the waves and Show or hide the sum. 
    </p>

    <p>
      If the one wave has the negative frequency of the other wave their sum oscialates in only one direction. The difference of their two phases determines that direction.
    </p>
    <p>
      If neither frequency nor amplitude match the resulting sum may look pretty wild. You can show only the sum and hide the two individual waves for a better view.
    </p>
  </details>

  <footer>
    <a href="https://tools.laszlokorte.de">More educational tools</a>
  </footer>
</div>

<Canvas>
  <Scene {hideParts} {freqA} {ampA} {phaseA} {freqB} {ampB} {phaseB} {showB} {showSum} />
</Canvas>
