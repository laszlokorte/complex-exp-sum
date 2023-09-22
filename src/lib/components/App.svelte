<script>
  import { Canvas } from '@threlte/core'
  import Scene from './Scene.svelte'
  import {onMount} from 'svelte'

  const numberFormatInt = new Intl.NumberFormat('en-US', { 
    minimumFractionDigits: 0,
    maximumFractionDigits: 0,
  })

  const numberFormatDecimal = new Intl.NumberFormat('en-US', { 
    minimumFractionDigits: 2,
    maximumFractionDigits: 2,
  })

  let polar = false
  let polarTight = false

  let playing = false
  let speedA = 1
  let speedB = 1

  let freqA = 5
  let ampA = 1
  let phaseA = 0

  let showSum = false
  let hideParts = false
  let showB = true
  let conjugate = false
  let lockPhase = false

  let freqB = -5
  let ampB = 1
  let phaseB = 0

  $: if(conjugate) {
    freqB = -freqA
    ampB = ampA
    phaseB = -1 * phaseA
  } else if(lockPhase) {
    phaseB = phaseA
    speedB = (Math.sign(freqB)*Math.sign(freqA) || 1) * speedA
  }

  onMount(() => {
    let frame = null

    const step = () => {
      frame = null

      if(playing) {
        phaseA = phaseA + (Math.sign(freqA)||1) * speedA / 100
        phaseB = phaseB + (Math.sign(freqB)||1) * speedB / 100
        while(phaseA > 1) {
          phaseA -= 2
        }
        while(phaseA < -1) {
          phaseA += 2
        }
        while(phaseB > 1) {
          phaseB -= 2
        }
        while(phaseB < -1) {
          phaseB += 2
        }
      }

      frame = requestAnimationFrame(step)
    }

    step()

    return () => {
      if(frame) {
        cancelAnimationFrame(frame)
      }
    }
  })
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
    grid-template-rows: auto auto;
    grid-auto-columns: auto;
    gap: 0 1em;
    align-items: start;
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
      <dt><label for="freqA">Frequency: {numberFormatInt.format(freqA)}Hz</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="-30" max="30" bind:value={freqA} id="freqA" /></dd>
      <dt><label for="ampA">Amplitude: {numberFormatDecimal.format(ampA)}</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="0" max="3" step="0.2" bind:value={ampA} id="ampA" /></dd>
      <dt><label for="phaseA">Phase: {numberFormatDecimal.format(phaseA)}</label></dt>
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
      <dt><label for="freqB">Frequency: {numberFormatInt.format(freqB)} Hz</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="-30" max="30" bind:value={freqB} id="freqB" /></dd>
      <dt><label for="ampB">Amplitude: {numberFormatDecimal.format(ampB)}</label></dt>
      <dd><input disabled={conjugate} style:accent-color="#fe3d00" type="range" min="0" max="3" step="0.2" bind:value={ampB} id="ampB" /></dd>
      <dt>
        <label for="phaseB">Phase: {numberFormatDecimal.format(phaseB)}</label></dt>
      <dd><input disabled={conjugate || lockPhase} style:accent-color="#fe3d00" type="range" min="-1" max="1" step="0.005" bind:value={phaseB} id="phaseB" />
        {#if !conjugate}
        <br>
        <label><input type="checkbox" style:accent-color="#fe3d00" bind:checked={lockPhase}> Same as A</label>
        {/if}
      </dd>
    </dl>
    {/if}
  </fieldset>

  <fieldset>
    <legend>Periodicity</legend>


   <div class="checkbox-list">
      <label>
        <input type="checkbox" bind:checked={polar}> Wrap time axis
      </label>
   </div>
    
      {#if polar}
      <p>
        For periodic signals (i.e. those having a discrete spectrum of integer frequencies) the time axis can be though thought of as folded into a circle with the circumference of the longest period, i.e. the inverse of the lowest frequence. For non-periodic signals the spectrum would be continuous making the lowest frequency infinitely  small and the radius infinitely  large, i.e. stretching  it back into a line.
      </p>

     <div class="checkbox-list">
        <label>
          <input type="checkbox" bind:checked={polarTight}> Fold as tight as possible
        </label>
     </div>
      {/if}
  </fieldset>

   <fieldset>
    <legend>Play</legend>


   <div class="checkbox-list">
      <label>
        <input  type="checkbox" bind:checked={playing}> Animate Phase
      </label>
      {#if playing}
      <dl>
      <dt><label for="speedB">Speed A: {numberFormatDecimal.format(speedA)}</label></dt>
      <dd><input style:accent-color="#3dfe00" type="range" min="-1" step="0.01" max="1" bind:value={speedA} id="speedB" /></dd>
      <dt><label for="speedB">Speed B: {numberFormatDecimal.format(speedB)}</label></dt>
      <dd><input disabled={lockPhase || conjugate} style:accent-color="#fe3d00" type="range" min="-1" step="0.01" max="1" bind:value={speedB} id="speedB" /></dd>
      </dl>
      {/if}
   </div>
    
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
      Explore how two complex waves/oscillations interfere de- or constructively. Use the controls above to set the frequency, amplitude and phase of the waves. Enable either both or only one of the waves and show or hide the sum. 
    </p>

    <p>
      If the one wave has the opposite(negative) frequency of the other wave their sum oscialates in only one direction because the other direction is cancelled by destructive interference. The difference of their two phases determines the direction in which the interference occurrs. Changing the phase of only one of the summed waves results in the sum to be rotated.
    </p>
    <p>
      If neither frequency nor amplitude of the two waves match then their sum may look pretty wild. You may enable only the sum and hide the two individual waves for a better view.
    </p>
  </details>

  <footer>
    <a href="https://tools.laszlokorte.de">More educational tools</a>
  </footer>
</div>

<Canvas>
  <Scene {polarTight} {polar} {hideParts} {freqA} {ampA} {phaseA} {freqB} {ampB} {phaseB} {showB} {showSum} />
</Canvas>
