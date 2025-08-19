<script lang="ts">
  import Layout from '$lib/components/Layout.svelte';

  const autorInfo = {
    nome: 'Galileo Galilei',
    url: 'https://www.britannica.com/biography/Galileo-Galilei'
  };
</script>

<Layout titulo="Queda Livre" autor={autorInfo}>
  <div slot="formulas">
    <div class="border border-gray-300 rounded-md p-3">
      <h3 class="font-bold text-center text-blue-700">Modelo Simplificado</h3>
      <p class="text-center mt-2 text-sm"><code>s = s₀ + v₀t + ½gt²</code></p>
    </div>
    <div class="border border-gray-300 rounded-md p-3">
      <h3 class="font-bold text-center text-blue-700">Modelo Real</h3>
      <p class="text-center mt-2 text-sm"><code>F = mg - kv</code></p>
    </div>
    <div class="border border-gray-300 rounded-md p-3">
      <h3 class="font-bold text-center text-blue-700">Variáveis</h3>
      <ul class="list-disc list-inside mt-2 text-sm text-black">
        <li>s: posição</li>
        <li>s₀: posição inicial</li>
        <li>v₀: velocidade inicial</li>
        <li>t: tempo</li>
        <li>g: gravidade</li>
        <li>F: força</li>
        <li>m: massa</li>
        <li>k: resistência</li>
      </ul>
    </div>
  </div>

  <div slot="parametros">
    {#each ['posição (s)', 'posição inicial (s₀)', 'velocidade inicial (v₀)', 'tempo (t)', 'gravidade (g)', 'força (F)', 'massa (m)', 'resistência (k)'] as label}
      <label class="block text-sm">
        {label}
        <input type="number" class="mt-1 w-full border border-gray-300 rounded px-2 py-1" />
      </label>
    {/each}
  </div>

  <div slot="simulacao">
    <div class="mt-4 border-2 border-dashed border-gray-300 rounded flex items-center justify-center h-32">
      <p class="text-gray-500">Área de Simulação</p>
    </div>
    <div class="flex justify-center gap-4 mt-4">
      <button class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">Iniciar</button>
      <button class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600">Reiniciar</button>
    </div>
  </div>

  <div slot="resultados">
    <h3 class="font-bold">Gráfico 1</h3>
    <canvas class="w-full h-40 bg-white rounded shadow"></canvas>
    <h3 class="font-bold">Gráfico 2</h3>
    <canvas class="w-full h-40 bg-white rounded shadow"></canvas>
  </div>

  <div slot="modal-content" class="relative w-full flex flex-col px-4 mt-auto mb-auto">
    <div class="flex items-center w-full mb-4">
      <span class="text-xs text-gray-700 mr-2 flex-shrink-0">1564</span>
      <div class="flex-1 h-0.5 bg-gray-300"></div>
      <span class="text-xs text-gray-700 ml-2 flex-shrink-0">1642</span>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 text-center">
      <div class="flex flex-col items-center">
        <div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center mb-2">
          1609
        </div>
        <h3 class="font-semibold text-sm">Telescópio</h3>
        <p class="text-xs text-gray-600">Galileu aperfeiçoa o telescópio e inicia observações astronômicas.</p>
      </div>

      <div class="flex flex-col items-center">
        <div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center mb-2">
          1616
        </div>
        <h3 class="font-semibold text-sm">Advertência</h3>
        <p class="text-xs text-gray-600">Advertido pela Inquisição por defender o heliocentrismo.</p>
      </div>

      <div class="flex flex-col items-center">
        <div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center mb-2">
          1633
        </div>
        <h3 class="font-semibold text-sm">Condenação</h3>
        <p class="text-xs text-gray-600">Condenado à prisão domiciliar após julgamento pela Inquisição.</p>
      </div>
    </div>
  </div>
</Layout>