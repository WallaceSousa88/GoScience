<script lang="ts">
  import Layout from '$lib/components/Layout.svelte';

  const titulo = 'Queda Livre';

  const autorInfo = {
    nome: 'Galileo Galilei',
    url: 'https://www.britannica.com/biography/Galileo-Galilei'
  };

  type UnidadeTipo = 's' | 'v' | 't' | 'g' | 'm' | 'k' | 'F';

  type Unidades = {
    s: Record<'m' | 'ft' | 'in', number>;
    v: Record<'m/s' | 'ft/s' | 'in/s', number>;
    t: Record<'s' | 'ms', number>;
    g: Record<'m/s²' | 'ft/s²', number>;
    m: Record<'kg' | 'lb', number>;
    k: Record<'kg/s' | 'lb/s', number>;
    F: Record<'N' | 'lbf', number>;
  };

  const unidades: Unidades = {
    s: { m: 1, ft: 0.3048, in: 0.0254 },
    v: { 'm/s': 1, 'ft/s': 0.3048, 'in/s': 0.0254 },
    t: { s: 1, ms: 0.001 },
    g: { 'm/s²': 1, 'ft/s²': 0.3048 },
    m: { kg: 1, lb: 0.453592 },
    k: { 'kg/s': 1, 'lb/s': 0.453592 },
    F: { N: 1, lbf: 4.44822 }
  };

  let s: number | null = 0;
  let sUnit = 'm';
  let s0: number | null = 10;
  let s0Unit = 'm';
  let v0: number | null = 0;
  let v0Unit = 'm/s';
  let t: number | null = null;
  let tUnit = 's';
  let g: number | null = -9.8;
  let gUnit = 'm/s²';
  let m: number | null = null;
  let mUnit = 'kg';
  let k: number | null = null;
  let kUnit = 'kg/s';
  let F: number | null = null;
  let FUnit = 'N';

  let resultado = '';
  let variavelCalculada: string | null = null;

  const alturaSimulacaoPx = 128;
  let posicaoY = 0;
  let sFinalExibido: { valor: number | null, unidade: string } = { valor: s, unidade: sUnit };

  let startTime: number | null = null;
  let animationFrame: number | null = null;

  function toSI(value: number | null, unit: string, type: UnidadeTipo): number | null {
    if (value === null) return null;
    const factor = unidades[type][unit as keyof typeof unidades[typeof type]];
    return value * factor;
  }

  function fromSI(value: number, unit: string, type: UnidadeTipo): number {
    const factor = unidades[type][unit as keyof typeof unidades[typeof type]];
    return value / factor;
  }

  function formatarResultado(_: 'Simplificado' | 'Real', variavel: string, valor: number, unidade: string) {
    return `${variavel} = ${valor.toFixed(2)} ${unidade}`;
  }

  function iniciarSimulacao() {
    if (animationFrame) cancelAnimationFrame(animationFrame);

    const sSI = toSI(s, sUnit, 's');
    const s0SI = toSI(s0, s0Unit, 's');
    const v0SI = toSI(v0, v0Unit, 'v');
    const tSI = toSI(t, tUnit, 't');
    const gSI = toSI(g, gUnit, 'g');
    const mSI = toSI(m, mUnit, 'm');
    const kSI = toSI(k, kUnit, 'k');
    const FSI = toSI(F, FUnit, 'F');

    let tempoTotalSI: number | null = null;
    let sFinalSI: number | null = sSI;

    const usandoModeloReal = mSI !== null || kSI !== null || FSI !== null;

    if (usandoModeloReal) {
      if (mSI !== null && gSI !== null && v0SI !== null && kSI !== null) {
        const Fcalc = mSI * gSI - kSI * v0SI;
        resultado = formatarResultado('Real', 'F', fromSI(Fcalc, FUnit, 'F'), FUnit);
        variavelCalculada = 'F';
      } else {
        resultado = 'Modelo Real: Preencha m, g, v₀ e k para calcular F.';
        return;
      }
    } else {
      if (sSI === null && s0SI !== null && v0SI !== null && tSI !== null && gSI !== null) {
        const sCalc = s0SI + v0SI * tSI + 0.5 * gSI * tSI * tSI;
        sFinalSI = sCalc;
        tempoTotalSI = tSI;
        resultado = formatarResultado('Simplificado', 's', fromSI(sCalc, sUnit, 's'), sUnit);
        variavelCalculada = 's';
      } else if (s0SI === null && sSI !== null && v0SI !== null && tSI !== null && gSI !== null) {
        const s0Calc = sSI - v0SI * tSI - 0.5 * gSI * tSI * tSI;
        tempoTotalSI = tSI;
        resultado = formatarResultado('Simplificado', 's₀', fromSI(s0Calc, s0Unit, 's'), s0Unit);
        variavelCalculada = 's₀';
      } else if (v0SI === null && sSI !== null && s0SI !== null && tSI !== null && gSI !== null) {
        if (tSI === 0) { resultado = 'O tempo não pode ser zero.'; return; }
        const vCalc = (sSI - s0SI - 0.5 * gSI * tSI * tSI) / tSI;
        tempoTotalSI = tSI;
        resultado = formatarResultado('Simplificado', 'v₀', fromSI(vCalc, v0Unit, 'v'), v0Unit);
        variavelCalculada = 'v₀';
      } else if (tSI === null && sSI !== null && s0SI !== null && v0SI !== null && gSI !== null) {
        const a = 0.5 * gSI;
        const b = v0SI;
        const c = s0SI - sSI;
        const delta = b * b - 4 * a * c;
        if (delta < 0) { resultado = 'Não há solução real para o tempo.'; return; }
        const tCalc1 = (-b + Math.sqrt(delta)) / (2 * a);
        const tCalc2 = (-b - Math.sqrt(delta)) / (2 * a);
        const tCalc = Math.max(tCalc1, tCalc2);
        if (tCalc < 0) { resultado = 'O tempo calculado é negativo.'; return; }
        tempoTotalSI = tCalc;
        resultado = formatarResultado('Simplificado', 't', fromSI(tCalc, tUnit, 't'), tUnit);
        variavelCalculada = 't';
      } else if (gSI === null && sSI !== null && s0SI !== null && v0SI !== null && tSI !== null) {
        if (tSI * tSI === 0) { resultado = 'O tempo não pode ser zero.'; return; }
        const gCalc = 2 * (sSI - s0SI - v0SI * tSI) / (tSI * tSI);
        tempoTotalSI = tSI;
        resultado = formatarResultado('Simplificado', 'g', fromSI(gCalc, gUnit, 'g'), gUnit);
        variavelCalculada = 'g';
      } else {
        resultado = 'Preencha ao menos 4 variáveis do modelo simplificado.';
        return;
      }
    }

    if (tempoTotalSI === null || s0SI === null || sFinalSI === null) return;

    sFinalExibido = { valor: fromSI(sFinalSI, sUnit, 's'), unidade: sUnit };

    startTime = null;

    function animar(timestamp: number) {
      if (!startTime) startTime = timestamp;
      const elapsed = (timestamp - startTime) / 1000;

      if (elapsed >= tempoTotalSI!) {
        posicaoY = alturaSimulacaoPx;
        if (animationFrame) cancelAnimationFrame(animationFrame);
        animationFrame = null;
        return;
      }

      const sAtual = s0SI! + v0SI! * elapsed + 0.5 * gSI! * elapsed * elapsed;
      const distanciaTotal = s0SI! - sFinalSI!;
      const distanciaPercorrida = s0SI! - sAtual;

      let fracaoPercorrida = 0;
      if (distanciaTotal !== 0) {
        fracaoPercorrida = distanciaPercorrida / distanciaTotal;
      }

      posicaoY = fracaoPercorrida * alturaSimulacaoPx;

      animationFrame = requestAnimationFrame(animar);
    }

    animationFrame = requestAnimationFrame(animar);
  }

  function reiniciarSimulacao() {
    resultado = '';
    variavelCalculada = null;
    posicaoY = 0;
    sFinalExibido = { valor: s, unidade: sUnit };
    startTime = null;
    if (animationFrame) {
        cancelAnimationFrame(animationFrame);
        animationFrame = null;
    }
  }

  function destaque(campo: string) {
    return variavelCalculada === campo ? 'border-2 border-blue-600/85 bg-blue-100/85' : '';
  }
</script>

<Layout {titulo} autor={autorInfo}>
  <svelte:fragment slot="formulas">
    <div class="border border-gray-300 rounded-md p-3">
      <h3 class="font-bold text-center text-blue-600/85">Modelo Simplificado</h3>
      <p class="text-center mt-2 text-sm"><code>s = s₀ + v₀t + ½gt²</code></p>
    </div>
    <div class="border border-gray-300 rounded-md p-3">
      <h3 class="font-bold text-center text-blue-600/85">Modelo Real</h3>
      <p class="text-center mt-2 text-sm"><code>F = mg - kv</code></p>
    </div>
  </svelte:fragment>

  <svelte:fragment slot="parametros">
    <div class="flex-1 overflow-y-auto pr-2 -mr-2 space-y-2">
      <label class="block text-sm">
        posição (s)
        <div class="flex gap-2">
          <input type="number" bind:value={s} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('s')}`} />
          <select bind:value={sUnit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="m">m</option>
            <option value="ft">ft</option>
            <option value="in">in</option>
          </select>
        </div>
      </label>
      <label class="block text-sm">
        posição inicial (s₀)
        <div class="flex gap-2">
          <input type="number" bind:value={s0} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('s₀')}`} />
          <select bind:value={s0Unit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="m">m</option>
            <option value="ft">ft</option>
            <option value="in">in</option>
          </select>
        </div>
      </label>
      <label class="block text-sm">
        velocidade inicial (v₀)
        <div class="flex gap-2">
          <input type="number" bind:value={v0} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('v₀')}`} />
          <select bind:value={v0Unit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="m/s">m/s</option>
            <option value="ft/s">ft/s</option>
            <option value="in/s">in/s</option>
          </select>
        </div>
      </label>
      <label class="block text-sm">
        tempo (t)
        <div class="flex gap-2">
          <input type="number" bind:value={t} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('t')}`} />
          <select bind:value={tUnit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="s">s</option>
            <option value="ms">ms</option>
          </select>
        </div>
      </label>
      <label class="block text-sm">
        gravidade (g)
        <div class="flex gap-2">
          <input type="number" bind:value={g} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('g')}`} />
          <select bind:value={gUnit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="m/s²">m/s²</option>
            <option value="ft/s²">ft/s²</option>
          </select>
        </div>
      </label>
       <label class="block text-sm">
        força (F)
        <div class="flex gap-2">
          <input type="number" bind:value={F} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('F')}`} />
          <select bind:value={FUnit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="N">N</option>
            <option value="lbf">lbf</option>
          </select>
        </div>
      </label>
      <label class="block text-sm">
        massa (m)
        <div class="flex gap-2">
          <input type="number" bind:value={m} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('m')}`} />
          <select bind:value={mUnit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="kg">kg</option>
            <option value="lb">lb</option>
          </select>
        </div>
      </label>
      <label class="block text-sm">
        resistência (k)
        <div class="flex gap-2">
          <input type="number" bind:value={k} class={`mt-1 w-2/3 border border-gray-300 rounded px-2 py-1 ${destaque('k')}`} />
          <select bind:value={kUnit} class="mt-1 w-1/3 border border-gray-300 rounded px-2 py-1">
            <option value="kg/s">kg/s</option>
            <option value="lb/s">lb/s</option>
          </select>
        </div>
      </label>
    </div>
  </svelte:fragment>

  <svelte:fragment slot="simulacao">
    <div class="flex flex-col flex-1 mt-4">
      <div class="border-2 border-dashed border-gray-300 rounded relative overflow-hidden flex-1" style="height: {alturaSimulacaoPx}px;">

        <div class="absolute left-2 top-2 text-xs text-gray-700">s₀ = {s0 ?? '?'} {s0Unit}</div>

        <div class="absolute left-2 bottom-2 text-xs text-gray-700">
          s = {sFinalExibido.valor !== null ? sFinalExibido.valor.toFixed(2) : '?'} {sFinalExibido.unidade}
        </div>

        <div
          class="w-6 h-6 bg-green-600/85 rounded-full absolute left-1/2 transform -translate-x-1/2"
          style="top: {posicaoY}px;"
        ></div>
      </div>

      <div class="grid grid-cols-3 gap-2 mt-4 items-center flex-none">
          <button class="h-10 bg-blue-600/85 text-white px-2 py-2 rounded hover:bg-blue-700/85 flex items-center justify-center" on:click={iniciarSimulacao}>
            Calcular
          </button>
          <button class="h-10 bg-red-600/85 text-white px-2 py-2 rounded hover:bg-red-700/85 flex items-center justify-center" on:click={reiniciarSimulacao}>
            Reiniciar
          </button>
        <div class="h-10 border border-gray-300 rounded px-3 py-2 text-sm text-gray-700 flex items-center justify-center overflow-hidden text-center">
          {resultado || '...'}
        </div>
      </div>
    </div>
  </svelte:fragment>

   <svelte:fragment slot="resultados">
    <div class="flex flex-col h-full space-y-2">
      <div>
        <h3 class="font-bold text-sm mb-1">Gráfico Posição x Tempo</h3>
        <canvas class="w-full h-full bg-gray-50 rounded shadow min-h-0"></canvas>
      </div>
      <div >
        <h3 class="font-bold text-sm mb-1">Gráfico Velocidade x Tempo</h3>
        <canvas class="w-full h-full bg-gray-50 rounded shadow min-h-0"></canvas>
      </div>
    </div>
  </svelte:fragment>

  <div slot="modal-content" class="relative w-full flex flex-col px-4 mt-auto mb-auto">
    <div class="flex items-center w-full mb-4">
      <span class="text-xs text-gray-700 mr-2 flex-shrink-0">1564</span>
      <div class="flex-1 h-0.5 bg-gray-300"></div>
      <span class="text-xs text-gray-700 ml-2 flex-shrink-0">1642</span>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 text-center">
      <div class="flex flex-col items-center">
        <div class="bg-blue-600/85 text-white rounded-full w-8 h-8 flex items-center justify-center mb-2">1609</div>
        <h3 class="font-semibold text-sm">Telescópio</h3>
        <p class="text-xs text-gray-600">Galileu aperfeiçoa o telescópio e inicia observações astronômicas.</p>
      </div>
      <div class="flex flex-col items-center">
        <div class="bg-blue-600/85 text-white rounded-full w-8 h-8 flex items-center justify-center mb-2">1616</div>
        <h3 class="font-semibold text-sm">Advertência</h3>
        <p class="text-xs text-gray-600">Advertido pela Inquisição por defender o heliocentrismo.</p>
      </div>
      <div class="flex flex-col items-center">
        <div class="bg-blue-600/85 text-white rounded-full w-8 h-8 flex items-center justify-center mb-2">1633</div>
        <h3 class="font-semibold text-sm">Condenação</h3>
        <p class="text-xs text-gray-600">Condenado à prisão domiciliar após julgamento pela Inquisição.</p>
      </div>
    </div>
  </div>
</Layout>