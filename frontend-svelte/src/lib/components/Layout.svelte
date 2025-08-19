<script lang="ts">
  import Icon from '@iconify/svelte';
  import { tick, onDestroy, onMount } from 'svelte';

  export let titulo: string;
  export let autor: { nome: string; url: string };

  let modalAberto = false;

  let botaoSaibaMais: HTMLButtonElement | null = null;
  let overlay: HTMLDivElement | null = null;
  let conteudoModal: HTMLDivElement | null = null;
  let tituloModalId = `titulo-modal-${titulo.toLowerCase().replace(/\s/g, '-')}`;

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

<div class="flex flex-col flex-1 overflow-hidden">
  <div class="flex-none flex flex-col items-center justify-center gap-2 px-4 py-4">
    <h1 class="text-3xl font-bold flex items-center gap-2 text-black">
      <Icon icon="hugeicons:hot-air-balloon" class="w-8 h-8" />
      {titulo}
      <Icon icon="hugeicons:hot-air-balloon" class="w-8 h-8" />
    </h1>
    <p class="text-black">
      <a href={autor.url} target="_blank" class="flex items-center gap-1 hover:underline">
        Autor: {autor.nome}
        <Icon icon="mdi:open-in-new" class="w-5 h-5" />
      </a>
    </p>
    <button class="text-blue-500 hover:underline" on:click={abrirModal} bind:this={botaoSaibaMais}>
      Saiba mais
    </button>
  </div>

  <div class="flex-1 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 px-4 pb-4 overflow-hidden">
    <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
      <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
        <Icon icon="tabler:math-function" class="w-6 h-6" />
        Fórmulas
      </h2>
      <div class="mt-4 space-y-4">
        <slot name="formulas" />
      </div>
    </div>

    <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
      <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
        <Icon icon="tabler:settings" class="w-6 h-6" />
        Parâmetros
      </h2>
      <div class="mt-4 space-y-4">
        <slot name="parametros" />
      </div>
    </div>

    <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
      <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
        <Icon icon="ix:project-simulation" class="w-6 h-6" />
        Simulação
      </h2>
      <slot name="simulacao" />
    </div>

    <div class="bg-white rounded-lg shadow p-4 flex flex-col overflow-hidden">
      <h2 class="flex items-center gap-2 text-lg font-semibold text-black">
        <Icon icon="tabler:chart-bar" class="w-6 h-6" />
        Resultados
      </h2>
      <div class="mt-4 space-y-4">
        <slot name="resultados" />
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

        <h2 id={tituloModalId} class="sr-only">Resumo — {autor.nome}</h2>

        <slot name="modal-content" />
      </div>
    </div>
  {/if}
</div>