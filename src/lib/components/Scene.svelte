<script>
  import { T } from '@threlte/core'
  import {
    MeshLineMaterial,
    MeshLineGeometry,
    Grid,
    OrbitControls,
    useTexture
  } from '@threlte/extras'
  import { Vector3, CatmullRomCurve3 } from 'three'

  export let samples = 1000
  export let perspective = true

  export let freqA = 1
  export let ampA = 1
  export let phaseA = 0

  export let showB = true
  export let freqB = 1
  export let ampB = 1
  export let phaseB = 0

  export let showSum = true
  export let hideParts = false


  $: pointsA = Array(samples).fill(null).map((_,i,a) => (i/a.length - 0.5)*2).map((x) => {
    return new Vector3(x*40, Math.cos(x*Math.PI*2*freqA + phaseA*Math.PI*2)*ampA*3, Math.sin(x*Math.PI*2*freqA + phaseA*Math.PI*2)*ampA*3)
  })
  $: pointsB = Array(samples).fill(null).map((_,i,a) => (i/a.length - 0.5)*2).map((x) => {
    return new Vector3(x*40, Math.cos(x*Math.PI*2*freqB + phaseB*Math.PI*2)*ampB*3, Math.sin(x*Math.PI*2*freqB + phaseB*Math.PI*2)*ampB*3)
  })

  $: pointsCombined = Array(samples).fill(null).map((_,i,a) => (i/a.length - 0.5)*2).map((x,i) => {
    return new Vector3(x*40, pointsA[i].y+pointsB[i].y, pointsA[i].z+pointsB[i].z)
  })
</script>


<Grid
  gridSize={[80, 80]}
  cellColor={'#46536b'}
  sectionColor={'#46536b'}
  sectionThickness={1}
  depthWrite={false}
  depthTest={true}
/>

{#if !showSum || !hideParts}

{#if showB}
<T.Mesh>
  <MeshLineGeometry
    points={pointsB}
  />

  <MeshLineMaterial
    width={4}
    attenuate={false}
    scaleDown={0}
    color="#fe3d00"
  />
</T.Mesh>
{/if}

<T.Mesh>
  <MeshLineGeometry
    points={pointsA}
  />

  <MeshLineMaterial
    width={4}
    attenuate={false}
    scaleDown={0}
    color="#3dfe00"
  />
</T.Mesh>

{/if}

{#if showB && showSum}
<T.Mesh>
  <MeshLineGeometry
    points={pointsCombined}
  />

  <MeshLineMaterial
    width={4}
    attenuate={false}
    scaleDown={0}
    color="#fff"
  />
</T.Mesh>
{/if}

<T.PerspectiveCamera
  makeDefault={perspective}
  on:create={({ ref }) => {
    ref.position.set(2, 5, 50)
  }}
>
  <OrbitControls
    autoRotate={false}
    autoRotateSpeed={2}
    enableDamping
    target.y={3}
  />
</T.PerspectiveCamera>

