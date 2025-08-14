<script lang="ts">
  import Icon from '@iconify/svelte';
  import { tick, onDestroy, onMount } from 'svelte';

  let modalAberto = false;

  let botaoSaibaMais: HTMLButtonElement | null = null;
  let overlay: HTMLDivElement | null = null;
  let conteudoModal: HTMLDivElement | null = null;
  let tituloModalId = 'titulo-modal-queda-livre';

  let elementoFocoAnterior: HTMLElement | null = null;
  let overflowAnterior = '';

  function abrirModal() {
    elementoFocoAnterior = typeof document !== 'undefined' ? (document.activeElement as HTMLElement | null) : null;
    modalAberto = true;
    focusPrimeiroElemento();
    bloquearScroll();
  }

  function fecharModal() {
    modalAberto = false;
    elementoFocoAnterior?.focus?.();
    restaurarScroll();
  }

  function handleCliqueFora(event: MouseEvent) {
    if (event.target === event.currentTarget) fecharModal();
  }

  function bloquearScroll() {
    if (typeof document === 'undefined') return;
    overflowAnterior = document.body.style.overflow;
    document.body.style.overflow = 'hidden';
  }

  function restaurarScroll() {
    if (typeof document === 'undefined') return;
    document.body.style.overflow = overflowAnterior ?? '';
  }

  onDestroy(() => {
    if (modalAberto) restaurarScroll();
    document.removeEventListener('keydown', onJanelaKeydown);
  });

  onMount(() => {
    document.addEventListener('keydown', onJanelaKeydown);
  });

  function trapFocus(e: KeyboardEvent) {
    if (e.key !== 'Tab') return;
    if (!conteudoModal) return;

    const focusables = conteudoModal.querySelectorAll<HTMLElement>(
      [
        'a[href]',
        'area[href]',
        'button:not([disabled])',
        'input:not([disabled]):not([type="hidden"])',
        'select:not([disabled])',
        'textarea:not([disabled])',
        '[tabindex]:not([tabindex="-1"])'
      ].join(', ')
    );

    if (!focusables.length) {
      e.preventDefault();
      conteudoModal.focus();
      return;
    }

    const first = focusables[0];
    const last = focusables[focusables.length - 1];
    const active = document.activeElement as HTMLElement | null;
    const indoVolta = e.shiftKey;

    if (!indoVolta && active === last) {
      e.preventDefault();
      first.focus();
    } else if (indoVolta && active === first) {
      e.preventDefault();
      last.focus();
    }
  }

  async function focusPrimeiroElemento() {
    await tick();
    if (!conteudoModal) return;

    const primeiroFocavel = conteudoModal.querySelector<HTMLElement>(
      [
        'button:not([disabled])',
        'a[href]',
        '[tabindex]:not([tabindex="-1"])',
        'input:not([disabled]):not([type="hidden"])',
        'select:not([disabled])',
        'textarea:not([disabled])'
      ].join(', ')
    );

    if (primeiroFocavel) {
      primeiroFocavel.focus();
    } else {
      overlay?.focus();
    }
  }

  function onJanelaKeydown(e: KeyboardEvent) {
    if (modalAberto && e.key === 'Escape') fecharModal();
  }
</script>

<svelte:window on:keydown={onJanelaKeydown} />

<div class="flex flex-col flex-1 overflow-hidden h-full">
  <div class="flex-none flex flex-col items-center justify-center gap-2 px-4 py-4">
    <h1 class="text-3xl font-bold flex items-center gap-2 text-black">
      <Icon icon="hugeicons:hot-air-balloon" class="w-8 h-8" />
      Queda Livre
      <Icon icon="hugeicons:hot-air-balloon" class="w-8 h-8" />
    </h1>
    <p class="text-black">
      <a href="https://www.britannica.com/biography/Galileo-Galilei" target="_blank" class="flex items-center gap-1 hover:underline">
        Autor: Galileo Galilei
        <Icon icon="mdi:open-in-new" class="w-5 h-5" />
      </a>
    </p>
    <button class="text-blue-500 hover:underline" on:click={abrirModal} bind:this={botaoSaibaMais}>
      Saiba mais
    </button>
  </div>

  <div class="flex-1 overflow-hidden px-4 pb-4">
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 h-full">
      <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="tabler:math-function" class="w-6 h-6" />
          Fórmulas
        </h2>
        <div class="mt-4 space-y-4">
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

      <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="tabler:settings" class="w-6 h-6" />
          Parâmetros
        </h2>
        <div class="mt-4 space-y-4">
          {#each ['posição (s)', 'posição inicial (s₀)', 'velocidade inicial (v₀)', 'tempo (t)', 'gravidade (g)', 'força (F)', 'massa (m)', 'resistência (k)'] as label}
            <label class="block text-sm">
              {label}
              <input type="number" class="mt-1 w-full border border-gray-300 rounded px-2 py-1" />
            </label>
          {/each}
          <label class="block text-sm">
            <input type="checkbox" class="mr-2" />
            Resistência do ar
          </label>
        </div>
      </div>

      <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="ix:project-simulation" class="w-6 h-6" />
          Simulação
        </h2>
        <div class="mt-4 border-2 border-dashed border-gray-300 rounded flex items-center justify-center h-32">
          <p class="text-gray-500">Área de Simulação</p>
        </div>
        <div class="flex justify-center gap-4 mt-4">
          <button class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">Iniciar</button>
          <button class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600">Reiniciar</button>
        </div>
      </div>

      <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
        <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
          <Icon icon="tabler:chart-bar" class="w-6 h-6" />
          Resultados
        </h2>
        <div class="mt-4 space-y-4">
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
    aria-labelledby={tituloModalId}
    tabindex="0"
    bind:this={overlay}
    on:click={handleCliqueFora}
    on:keydown={trapFocus}
  >
    <div
      class="bg-white rounded-lg shadow-lg w-[90vw] sm:w-[66vw] h-[66vh] min-w-[320px] min-h-[200px] max-w-5xl max-h-[90vh] p-6 relative flex flex-col overflow-y-auto"
      bind:this={conteudoModal}
    >
      <button
        class="absolute top-4 right-4 text-gray-500 hover:text-black"
        on:click={fecharModal}
        aria-label="Fechar modal"
      >
        <Icon icon="heroicons:x-mark" class="w-6 h-6" />
      </button>

      <h2 id={tituloModalId} class="sr-only">Resumo — Galileu Galilei</h2>

      <div class="relative w-full flex flex-col px-4 mt-auto mb-auto">
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
    </div>
  </div>
{/if}

</div>
