<script lang="ts">
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';
  import Variable  from '../../../components/icons/Variable.svelte';
	import Beaker from '../../../components/icons/Beaker.svelte';
  import ChartBar from '../../../components/icons/ChartBar.svelte';
  import Calculator from '../../../components/icons/Calculator.svelte';
  import ExternalLink from '../../../components/ExternalLink.svelte';

  let s0 = 20;
  let v0 = 0;
  let g = 9.81;
  let m = 1;
  let k = 0.1;
  let resistenciaDoAr = false;

  let posicao = s0;
  let velocidade = v0;
  let tempo = 0;
  let intervalo: ReturnType<typeof setInterval>;

  let dadosPosicao: { x: number; y: number }[] = [];
  let dadosVelocidade: { x: number; y: number }[] = [];

  let canvasPosicao: HTMLCanvasElement;
  let canvasVelocidade: HTMLCanvasElement;
  let areaSimulacao: HTMLDivElement;
  let alturaArea = 300;

  let chartPosicao: Chart;
  let chartVelocidade: Chart;

  onMount(() => {
    if (areaSimulacao) {
      alturaArea = areaSimulacao.clientHeight;
    }
  });

  function iniciarSimulacao() {
    if (areaSimulacao) {
      alturaArea = areaSimulacao.clientHeight;
    }

    tempo = 0;
    posicao = s0;
    velocidade = v0;
    dadosPosicao = [];
    dadosVelocidade = [];

    if (chartPosicao) chartPosicao.destroy();
    if (chartVelocidade) chartVelocidade.destroy();

    intervalo = setInterval(() => {
      const dt = 0.05;
      const aceleracao = resistenciaDoAr ? (m * g - k * velocidade) / m : g;

      velocidade += aceleracao * dt;
      posicao = Math.max(0, posicao - velocidade * dt);
      tempo += dt;

      dadosPosicao.push({ x: tempo, y: posicao });
      dadosVelocidade.push({ x: tempo, y: velocidade });

      if (posicao <= 0) {
        clearInterval(intervalo);
        desenharGraficos();
      }
    }, 50);
  }

  function desenharGraficos() {
    chartPosicao = new Chart(canvasPosicao, {
      type: 'line',
      data: {
        datasets: [{
          label: 'Posição (m)',
          data: dadosPosicao,
          borderColor: 'red',
          backgroundColor: 'transparent',
          fill: false
        }]
      },
      options: {
        scales: {
          x: { title: { display: true, text: 'Tempo (s)' } },
          y: { title: { display: true, text: 'Posição (m)' } }
        }
      }
    });

    chartVelocidade = new Chart(canvasVelocidade, {
      type: 'line',
      data: {
        datasets: [{
          label: 'Velocidade (m/s)',
          data: dadosVelocidade,
          borderColor: '#457b9d',
          backgroundColor: 'transparent',
          fill: false
        }]
      },
      options: {
        scales: {
          x: { title: { display: true, text: 'Tempo (s)' } },
          y: { title: { display: true, text: 'Velocidade (m/s)' } }
        }
      }
    });
  }
</script>

<style>
  .objeto {
    width: 30px;
    height: 30px;
    background-color: red;
    border-radius: 50%;
    position: absolute;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
    transition: top 0.05s linear;
  }

  .area-simulacao {
    position: relative;
    height: 90%;
    width: 90%;
    margin: 5%;
    border: 2px dashed #ccc;
    overflow: hidden;
    background-color: white;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1.5rem;
    padding: 2rem;
  }

  .card {
    background-color: white;
    border-radius: 12px;
    padding: 1rem;
    box-shadow: 0 2px 8px rgba(0,0,0,0.05);
  }

  h2 {
    font-size: 1.5rem;
    color: black;
    margin-bottom: 0.5rem;
  }

  h3 {
    font-size: 1.1rem;
    color: black;
    margin-top: 1rem;
  }

  input {
    border: 1px solid rgba(0, 0, 0, 0.10);
    border-radius: 6px;
    padding: 0.4rem 0.6rem;
    background-color: white;
    color: black;
    font-size: 0.95rem;
    outline: none;
    transition: border-color 0.2s;
  }

  input:focus {
    border-color: black;
  }

  input, button {
    font-family: inherit;
    width: 100%;
    margin-top: 0.25rem;
    margin-bottom: 0.75rem;
  }

  button {
    background-color: red;
    color: white;
    padding: 0.5rem;
    border-radius: 6px;
    transition: background-color 0.3s;
  }

  button:hover {
    background-color: red;
  }

  canvas {
    max-width: 100%;
    background-color: white;
    border-radius: 8px;
  }

  label {
    display: block;
    font-size: 0.9rem;
    margin-top: 0.5rem;
    color: black;
    text-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
    border-bottom: 1px solid rgba(0, 0, 0, 0.05);
    padding-bottom: 4px;
  }

  .title {
  text-align: center;
  font-size: 2rem;
  margin: 2rem 0 0.5rem 0;
  color: black;
  }

</style>

<h1 class="title">🪂 Queda Livre</h1>
<p style="text-align: center;">
  <strong>Autor:</strong>
  <ExternalLink
    href="https://www.britannica.com/biography/Galileo-Galilei/Galileos-Copernicanism"
    text="Galileu Galilei (1564 - 1642)"
    size={16}
  />
</p>
<div class="grid">
  <div class="card">
    <h2 style="display: flex; align-items: center; gap: 0.5rem;">
      <Calculator size={25} />
      Fórmulas
    </h2>
    <div class="card-formulas">
      <h3 style="text-align: center;">Modelo Simplificado</h3><br>
      <p style="text-align: center;"><code>s = s₀ + v₀ t + ½ g  t²</code></p><br>
      <h3 style="text-align: center;">Modelo Real</h3><br>
      <p style="text-align: center;"><code>F = m g - k v</code></p><br><br>
      <ul>
        <li><strong>s₀:</strong> posição inicial (m)</li><br>
        <li><strong>v₀:</strong> velocidade inicial (m/s)</li><br>
        <li><strong>g:</strong> gravidade (m/s²)</li><br>
        <li><strong>t:</strong> tempo (s)</li><br>
        <li><strong>m:</strong> massa (kg)</li><br>
        <li><strong>k:</strong> constante de resistência (kg/s)</li><br>
      </ul>
    </div>
  </div>

  <div class="card">
    <h2 style="display: flex; align-items: center; gap: 0.5rem;">
      <Variable size={25} />
      Variáveis
    </h2>
    <h3 style="text-align: center;">Modelo Simplificado</h3>
    <label>Posição Inicial s₀ (m): <input type="number" bind:value={s0} /></label>
    <label>Velocidade Inicial v₀ (m/s): <input type="number" bind:value={v0} /></label>
    <label>Gravidade g (m/s²): <input type="number" step="0.01" bind:value={g} /></label>

    <h3 style="text-align: center;">Modelo Real</h3>
    <label>Massa m (kg): <input type="number" step="0.1" bind:value={m} /></label>
    <label>Constante de Resistência k (kg/s): <input type="number" step="0.01" bind:value={k} /></label>
    <label><input type="checkbox" bind:checked={resistenciaDoAr} /> Incluir resistência do ar</label>

    <button on:click={iniciarSimulacao}>Iniciar Simulação</button>
  </div>

  <div class="card">
    <h2 style="display: flex; align-items: center; gap: 0.5rem;">
      <Beaker size={25} />
      Simulação
    </h2>
    <div class="area-simulacao" bind:this={areaSimulacao}>
      <div
        class="objeto"
        style="top: {Math.min(alturaArea - 30, (1 - Math.min(posicao, s0) / s0) * alturaArea)}px; left: calc(50% - 15px);"
      ></div>
    </div>
  </div>

  <div class="card">
    <h2 style="display: flex; align-items: center; gap: 0.5rem;">
      <ChartBar size={25} />
      Gráficos
    </h2>
    <h3>Posição vs Tempo</h3>
    <canvas bind:this={canvasPosicao}></canvas>
    <h3>Velocidade vs Tempo</h3>
    <canvas bind:this={canvasVelocidade}></canvas>
  </div>
</div>
