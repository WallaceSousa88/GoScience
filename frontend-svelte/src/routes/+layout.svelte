<script lang="ts">

  import '../app.css';
  import LabVirtualIcon from '$lib/icons/LabVirtualIcon.svelte';
  import Menu from '$lib/icons/Menu.svelte';
  import Close from '$lib/icons/Close.svelte';
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
        <Close className="w-6 h-6 transform transition duration-300" />
      {:else}
        <Menu className="w-6 h-6 transform transition duration-300" />
      {/if}
    </button>

    <div class="flex items-center space-x-3 mx-auto md:mx-0 order-2 md:order-1">
      <a href="/" class="flex items-center space-x-2">
        <LabVirtualIcon className="w-10 h-10 text-blue-600" />
        <span class="text-xl font-bold text-gray-800">LabVirtual</span>
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

<main class="w-full px-6 py-6">
  <slot />
</main>
