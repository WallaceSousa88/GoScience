<script lang="ts">
  import '../app.css';
  import Icon from '@iconify/svelte';
  import { slide, fade } from 'svelte/transition';
  import { onMount, tick } from 'svelte';

  let menuAberto = false;
  let menuRef: HTMLElement;
  let toggleBtnRef: HTMLButtonElement;
  let primeiroLinkRef: HTMLAnchorElement;

  const navLinks = [
    { href: '/', label: 'Dashboard' },
    { href: '/Mobile', label: 'Mobile' },
    { href: '/servicos', label: 'Simulador Offline' },
    { href: '/Apoio', label: 'Apoio' }
  ];

  const setMenu = async (aberto: boolean) => {
    menuAberto = aberto;
    if (aberto) {
      await tick();
      primeiroLinkRef?.focus();
    }
  };

  onMount(() => {
    const clickFora = (e: MouseEvent) => {
      const alvo = e.target as Node;
      if (
        menuAberto &&
        menuRef &&
        !menuRef.contains(alvo) &&
        toggleBtnRef &&
        !toggleBtnRef.contains(alvo)
      ) {
        setMenu(false);
      }
    };

    const escPressionado = (e: KeyboardEvent) => {
      if (e.key === 'Escape') setMenu(false);
    };

    document.addEventListener('mousedown', clickFora);
    document.addEventListener('keydown', escPressionado);

    return () => {
      document.removeEventListener('mousedown', clickFora);
      document.removeEventListener('keydown', escPressionado);
    };
  });
</script>

<div class="flex flex-col min-h-screen">
  <header class="bg-white shadow-md text-black">
    <div class="w-full px-4 py-4 flex items-center justify-between flex-wrap">
      <button
        bind:this={toggleBtnRef}
        class="block md:hidden order-1 focus:outline focus:outline-blue-500 transition duration-300"
        on:click={(e) => {
          e.stopPropagation();
          setMenu(!menuAberto);
        }}
        aria-label="Abrir menu de navegação"
        aria-expanded={menuAberto}
        aria-controls="menu-mobile"
      >
        {#if menuAberto}
          <Icon icon="mdi:close" class="w-6 h-6 transform transition duration-300" />
        {:else}
          <Icon icon="mdi:menu" class="w-6 h-6 transform transition duration-300" />
        {/if}
      </button>

      <div class="flex items-center space-x-3 mx-auto md:mx-0 order-2 md:order-1">
        <a href="/" class="flex items-center space-x-2">
          <Icon icon="mdi:flask-outline" class="w-10 h-10 text-black" />
          <span class="text-xl font-bold text-black">LabVirtual</span>
        </a>
      </div>

      <nav class="hidden md:flex space-x-6 order-3" aria-hidden={menuAberto}>
        {#each navLinks as link}
          <a
            href={link.href}
            on:click={() => setMenu(false)}
            class="block text-black hover:text-blue-600 transition-colors duration-300 font-medium px-2 py-2 rounded focus:outline focus:outline-blue-500"
          >
            {link.label}
          </a>
        {/each}
      </nav>
    </div>

    {#if menuAberto}
      <nav
        id="menu-mobile"
        bind:this={menuRef}
        class="md:hidden px-4 pb-6 pt-2 space-y-4"
        aria-hidden={!menuAberto}
        in:slide={{ duration: 250 }}
        out:fade={{ duration: 200 }}
      >
        <a
          href={navLinks[0].href}
          on:click={() => setMenu(false)}
          bind:this={primeiroLinkRef}
          class="block text-black hover:text-blue-600 transition-colors duration-300 font-medium px-2 py-2 rounded focus:outline focus:outline-blue-500"
        >
          {navLinks[0].label}
        </a>
        {#each navLinks.slice(1) as link}
          <a
            href={link.href}
            on:click={() => setMenu(false)}
            class="block text-black hover:text-blue-600 transition-colors duration-300 font-medium px-2 py-2 rounded focus:outline focus:outline-blue-500"
          >
            {link.label}
          </a>
        {/each}
      </nav>
    {/if}
  </header>

  <main class="flex-1 flex flex-col items-center justify-center w-full px-6">
    <slot />
  </main>

  <footer class="w-full border-t border-gray-200 text-sm text-gray-600 bg-white">
    <div
      class="max-w-7xl mx-auto px-4 flex justify-center items-center gap-2 text-center"
      style="height: 40px; line-height: 1; white-space: nowrap; overflow: hidden;"
    >
      <span class="flex items-center">
        © 2025 Desenvolvido por
        <a
          href="https://github.com/WallaceSousa88/GoScience"
          target="_blank"
          rel="noopener noreferrer"
          class="inline-flex items-center gap-1 ml-1 text-inherit no-underline hover:underline"
          style="line-height: 1;"
        >
          WallaceSousa88
          <Icon icon="mdi:open-in-new" class="w-3 h-3" />
        </a>
      </span>
      <span class="text-gray-400 mx-1">|</span>
      <a
        href="https://www.example.com.br"
        target="_blank"
        rel="noopener noreferrer"
        class="inline-flex items-center gap-1 ml-1 text-inherit no-underline hover:underline"
        style="line-height: 1;"
      >
        Sugestões
        <Icon icon="mdi:open-in-new" class="w-3 h-3" />
      </a>
      <span class="text-gray-400 mx-1">|</span>
      <a
        href="https://www.example.com.br"
        target="_blank"
        rel="noopener noreferrer"
        class="inline-flex items-center gap-1 ml-1 text-inherit no-underline hover:underline"
        style="line-height: 1;"
      >
        Relatar bug
        <Icon icon="mdi:open-in-new" class="w-3 h-3" />
      </a>
    </div>
  </footer>
</div>