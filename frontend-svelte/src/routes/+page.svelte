<script lang="ts">
    let modalAberto: string | null = null;
    const materias: string[] = ['Física', 'Química', 'Matemática'];

    const abrirModal = (materia: string): void => {
        modalAberto = materia;
    };

    const fecharModal = (): void => {
        modalAberto = null;
    };

    const handleKeydown = (e: KeyboardEvent): void => {
        if (modalAberto && e.key === 'Escape') {
            fecharModal();
        }
    };

    const autoFocus = (node: HTMLElement) => {
        setTimeout(() => node.focus(), 0);
    };
</script>

<svelte:window on:keydown={handleKeydown} />

<div class="flex flex-col items-center px-4 py-8">
    <h1 class="text-4xl font-bold text-black mb-4">Bem-vindo!</h1>

    <p class="text-lg text-black text-center max-w-3xl mb-6">
        Explore conceitos de Física, Química e Matemática com simulações interativas.<br>
        Clique nos cards para começar sua jornada científica.
    </p>

    <div class="grid grid-cols-1 sm:grid-cols-3 gap-8 w-full max-w-5xl">
        {#each materias as materia}
            <button
                type="button"
                on:click={() => abrirModal(materia)}
                class="flex flex-col justify-center items-center p-6 h-64 rounded-xl shadow-xl cursor-pointer hover:scale-105 transition duration-300 ease-in-out text-white
                    {materia === 'Física' ? 'bg-purple-500' :
                     materia === 'Química' ? 'bg-emerald-500' :
                     'bg-orange-500'}"
            >
                <span class="text-4xl mb-2">
                    {materia === 'Física' ? '⚛️' : materia === 'Química' ? '🧪' : '📐'}
                </span>
                <h2 class="text-3xl font-bold">{materia}</h2>
                <p class="mt-4 text-lg text-center">Clique para explorar conteúdos.</p>
            </button>
        {/each}
    </div>

    <blockquote class="italic text-center text-black max-w-2xl mb-10">
        <br><br>“A imaginação frequentemente nos leva a mundos que nunca existiram.<br>
        Mas sem ela, não vamos a lugar nenhum.”<br />
        <span class="block mt-2 font-semibold text-black">— Carl Sagan</span>
    </blockquote>

    {#if modalAberto}
        <div
            role="button"
            tabindex="0"
            class="fixed inset-0 bg-white/30 backdrop-blur-sm flex items-center justify-center z-10"
            on:click={(e) => {
                if (e.currentTarget === e.target) {
                    fecharModal();
                }
            }}
            on:keydown={(e) => {
                if (e.key === 'Enter' || e.key === ' ') {
                    fecharModal();
                }
            }}
        >
            <div
                class="bg-white rounded-lg p-6 w-80 text-center shadow-2xl relative"
                role="dialog"
                aria-modal="true"
                aria-labelledby="modal-title"
                tabindex="-1"
                use:autoFocus
            >
                <button
                    class="absolute top-2 right-2 text-gray-500 hover:text-gray-700 text-xl font-bold"
                    on:click={fecharModal}
                    aria-label="Fechar modal"
                >
                    &times;
                </button>

                <h3 id="modal-title" class="text-xl font-bold mb-4">Conteúdos de {modalAberto}</h3>
                <ul class="space-y-2 text-left mb-4">
                    <li class="hover:underline cursor-pointer">Item 1</li>
                    <li class="hover:underline cursor-pointer">Item 2</li>
                    <li class="hover:underline cursor-pointer">Item 3</li>
                </ul>
            </div>
        </div>
    {/if}
</div>
