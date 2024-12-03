<script>
  import { onMount, onDestroy } from 'svelte';
  import * as THREE from 'three';
  import vertexShader from '../vertex.vert?raw'; // バーテックスシェーダのインポート
  import fragmentShader from '../fragment.frag?raw'; // フラグメントシェーダのインポート

  let canvas;

  onMount(() => {
    const scene = new THREE.Scene();

    // OrthographicCameraを作成
    const aspect = window.innerWidth / window.innerHeight;
    const camera = new THREE.OrthographicCamera(
      -aspect, // left
      aspect,  // right
      1,       // top
      -1,      // bottom
      0.1,     // near
      1000     // far
    );
    const renderer = new THREE.WebGLRenderer({ canvas });
    renderer.setSize(window.innerWidth, window.innerHeight);

    camera.position.z = 5;

    // シェーダ用のuniformを定義
    const uniforms = {
      resolution: { value: new THREE.Vector2(window.innerWidth, window.innerHeight) },
      globalTime: { value: 0.0 },
    };

    // Quadの作成 (PlaneGeometryを使用) + ShaderMaterial
    const geometry = new THREE.PlaneGeometry(2, 2); // 2x2の平面
    const material = new THREE.ShaderMaterial({
      vertexShader,  // バーテックスシェーダを指定
      fragmentShader, // フラグメントシェーダを指定
      uniforms,
    });
    const quad = new THREE.Mesh(geometry, material);
    scene.add(quad);

    // アニメーションループ
    function animate(time) {
      requestAnimationFrame(animate);

      // `time`を秒単位で更新
      uniforms.globalTime.value = time * 0.001;

      renderer.render(scene, camera);
    }

    animate();

    // リサイズ対応
    function handleResize() {
      const width = window.innerWidth;
      const height = window.innerHeight;
      renderer.setSize(width, height);

      // カメラのアスペクト比を調整
      const aspect = width / height;
      camera.left = -aspect;
      camera.right = aspect;
      camera.top = 1;
      camera.bottom = -1;
      camera.updateProjectionMatrix();

      // Uniformの解像度を更新
      uniforms.resolution.value.set(width, height);
    }

    // リスナーを登録
    window.addEventListener('resize', handleResize);

    // クリーンアップ
    return () => {
      window.removeEventListener('resize', handleResize);
      renderer.dispose();
    };
  });
</script>

<canvas bind:this={canvas}></canvas>

<style>
  canvas {
    display: block;
    width: 100%;
    height: 100%;
  }
</style>
