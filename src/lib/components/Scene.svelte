<script lang="ts">
  import { T, useFrame } from '@threlte/core'
  import { interactivity } from '@threlte/extras';
  import vertexShader from `../vertex.vert?raw`
  import fragmentShader from `../fragment.frag?raw`
  import { Clock, Vector2 } from 'three';

  const clock = new Clock();

  interactivity();
  let time = new Vector2(0, 0);
  let resolution = new Vector2(document.body.clientWidth, document.body.clientHeight);

  useFrame(() => 
  {
    time.set(clock.getElapsedTime(), 0);

    resolution.set(document.body.clientWidth, document.body.clientHeight);
  })
</script>

<T.OrthographicCamera
  zoom={50}
  makeDefault
  on:create={({ ref }) => {
    ref.lookAt(0, 0, 0)
  }}
/>

<T.Mesh>
  <T.PlaneGeometry />
  <T.ShaderMaterial
    {vertexShader}
    {fragmentShader}
    uniforms={{
      globalTime: {
        value : time
      },
      resolution: {
        value : resolution
      },
    }}
  />
</T.Mesh>
