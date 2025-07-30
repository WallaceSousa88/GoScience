<script lang="ts">
    import '../app.css';
    let menuAberto = false;
</script>

<header class="bg-white shadow-md">
    <div class="w-full px-4 py-4 flex items-center justify-between flex-wrap">
        <button
            class="block md:hidden text-black order-1"
            on:click={() => (menuAberto = !menuAberto)}
            aria-label="Abrir menu de navegação"
        >
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                    d="M4 6h16M4 12h16M4 18h16" />
            </svg>
        </button>

        <div class="flex items-center space-x-3 mx-auto md:mx-0 order-2 md:order-1">
            <a href="/" class="flex items-center space-x-2">
                <img src="/logo.png" alt="Logo GoScience" class="w-16 h-16" />
                <span class="text-xl font-bold text-gray-800">LabVirtual</span>
            </a>
        </div>

        <nav class="hidden md:flex space-x-6 order-3">
            <a href="/" class="text-black hover:text-blue-600 font-medium">Dashboard</a>
            <a href="/Mobile" class="text-black hover:text-blue-600 font-medium">Mobile</a>
            <a href="/servicos" class="text-black hover:text-blue-600 font-medium">Simulador Offline</a>
            <a href="/Apoio" class="text-black hover:text-blue-600 font-medium">Apoio</a>
        </nav>
    </div>

    {#if menuAberto}
        <nav class="md:hidden px-4 pb-4 space-y-2">
            <a href="/" class="block text-black hover:text-blue-600 font-medium">Dashboard</a>
            <a href="/Mobile" class="block text-black hover:text-blue-600 font-medium">Mobile</a>
            <a href="/servicos" class="block text-black hover:text-blue-600 font-medium">Simulador Offline</a>
            <a href="/Apoio" class="block text-black hover:text-blue-600 font-medium">Apoio</a>
        </nav>
    {/if}
</header>

<main class="w-full px-6 py-6">
    <slot />
</main>