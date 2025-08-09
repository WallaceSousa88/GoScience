<script lang="ts">
  import Icon from '@iconify/svelte';
  import { onDestroy } from 'svelte';

  let modalAberto = false;

  function fecharModal() {
    modalAberto = false;
  }

  function handleKeydown(event: KeyboardEvent) {
    if (event.key === 'Escape') fecharModal();
  }

  function handleCliqueFora(event: MouseEvent) {
    if (event.target === event.currentTarget) fecharModal();
  }

  function autoFocus(node: HTMLElement) {
    node.focus();
    return {};
  }

  let ouvinteTeclaAdicionado = false;
  $: {
    if (modalAberto && typeof window !== 'undefined' && !ouvinteTeclaAdicionado) {
      window.addEventListener('keydown', handleKeydown);
      ouvinteTeclaAdicionado = true;
    } else if (!modalAberto && typeof window !== 'undefined' && ouvinteTeclaAdicionado) {
      window.removeEventListener('keydown', handleKeydown);
      ouvinteTeclaAdicionado = false;
    }
  }

  onDestroy(() => {
    if (typeof window !== 'undefined' && ouvinteTeclaAdicionado) {
      window.removeEventListener('keydown', handleKeydown);
    }
  });
</script>

<div class="flex flex-col flex-1 overflow-hidden">
  <div class="flex-none flex flex-col items-center justify-center gap-2 px-4 py-4">
    <h1 class="text-3xl font-bold flex items-center gap-2 text-black">
      <Icon icon="hugeicons:hot-air-balloon" class="w-8 h-8" />
      Queda Livre
      <Icon icon="hugeicons:hot-air-balloon" class="w-8 h-8" />
    </h1>
    <p class="text-blue-600">
      <a href="https://example.com" target="_blank" class="flex items-center gap-1 hover:underline">
        Link Externo
        <Icon icon="mdi:open-in-new" class="w-5 h-5" />
      </a>
    </p>
    <button on:click={() => modalAberto = true} class="text-blue-500 hover:underline">
      Saiba mais
    </button>
  </div>

  <div class="flex-1 flex flex-col overflow-hidden px-4">
    <div class="grid flex-1 grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
      <div class="bg-white rounded-lg shadow p-4 flex flex-col h-full overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="tabler:math-function" class="w-6 h-6" />
          Fórmulas
        </h2>
        <div class="overflow-y-auto flex-1 mt-4 space-y-4">
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
      </div>

      <div class="bg-white rounded-lg shadow p-4 flex flex-col h-full overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="tabler:settings" class="w-6 h-6" />
          Parâmetros
        </h2>
        <div class="overflow-y-auto flex-1 mt-4 space-y-4">
          <label class="block text-sm">
            Valor Numérico:
            <input type="number" class="mt-1 w-full border border-gray-300 rounded px-2 py-1" />
          </label>
          <label class="block text-sm">
            <input type="checkbox" class="mr-2" />
            Ativar opção
          </label>
        </div>
      </div>

      <div class="bg-white rounded-lg shadow p-4 flex flex-col h-full overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="ix:project-simulation" class="w-6 h-6" />
          Simulação
        </h2>
        <div class="overflow-y-auto flex-1 mt-4 border-2 border-dashed border-gray-300 rounded flex items-center justify-center">
          <p class="text-gray-500">Área de Simulação</p>
        </div>
        <div class="flex justify-center gap-4 mt-4">
          <button class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">Iniciar</button>
          <button class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600">Reiniciar</button>
        </div>
      </div>

      <div class="bg-white rounded-lg shadow p-4 flex flex-col h-full overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="tabler:chart-bar" class="w-6 h-6" />
          Resultados
        </h2>
        <div class="overflow-y-auto flex-1 mt-4 space-y-4">
          <h3 class="font-bold">Gráfico 1</h3>
          <canvas class="w-full h-40 bg-white rounded shadow"></canvas>
          <h3 class="font-bold">Gráfico 2</h3>
          <canvas class="w-full h-40 bg-white rounded shadow"></canvas>
        </div>
      </div>
    </div>
  </div>

  {#if modalAberto}
    <div
      class="fixed inset-0 bg-white/30 backdrop-blur-sm flex items-center justify-center z-50"
      role="dialog"
      aria-modal="true"
      tabindex="0"
      use:autoFocus
      on:click={handleCliqueFora}
      on:keydown={handleKeydown}
    >
      <div class="bg-white rounded-lg shadow-lg w-[90vw] sm:w-[66vw] h-[66vh] min-w-[320px] min-h-[200px] max-w-5xl max-h-[90vh] p-6 relative flex flex-col justify-center">
        <button class="absolute top-4 right-4 text-gray-500 hover:text-black" on:click={fecharModal} aria-label="Fechar modal">
          <Icon icon="heroicons:x-mark" class="w-6 h-6" />
        </button>

        <div class="relative w-full h-32 flex flex-col justify-center px-4">
          <div class="flex items-center w-full">
            <span class="text-xs text-gray-700 mr-2 flex-shrink-0">1069</span>
            <div class="flex-1 h-0.5 bg-gray-300"></div>
            <span class="text-xs text-gray-700 ml-2 flex-shrink-0">1500</span>
          </div>

          <div class="grid grid-cols-3 gap-0 mt-6 text-center">
            {#each [1, 2, 3] as ponto}
              <div class="flex flex-col items-center">
                <div class="bg-blue-500 text-white rounded-full w-8 h-8 flex items-center justify-center mb-2">
                  {ponto}
                </div>
                <h3 class="font-semibold text-sm">Evento {ponto}</h3>
                <p class="text-xs text-gray-600">Descrição do evento {ponto}</p>
              </div>
            {/each}
          </div>
        </div>
      </div>
    </div>
  {/if}
</div>