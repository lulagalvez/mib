<script lang="ts">
    import "../global.css"
    import { onMount, onDestroy } from "svelte";
    import { tweened } from "svelte/motion";
    import { cubicOut } from "svelte/easing";
    import { browser } from "$app/environment";
    import { goto } from "$app/navigation";

    const rotation = tweened(0, {
        duration: 3000,
    });

    let displayImage = true;

    let currentCycle = 1; // Inicia en el primer ciclo
    let showReturnButton = false; // Controla la visibilidad del botón de retorno

    let keyDownTimes: { [key: string]: number } = { 'ArrowDown': 0, 'ArrowLeft': 0, 'ArrowRight': 0 };
    let keyPressStart: { [key: string]: number | null } = {};

    onMount(() => {
        const startCycle = () => {
            const interval = setInterval(() => {
                rotation.update(n => n + 360);
            }, 3000);

            setTimeout(() => {
                clearInterval(interval);
                displayImage = false;

                // Inicia escucha de eventos de teclado solo si es necesario
                if (browser && currentCycle <= 3) {
                    window.addEventListener('keydown', handleKeyDown);
                    window.addEventListener('keyup', handleKeyUp);
                }

                // Espera 30 segundos más para cambiar de imagen o terminar el ciclo
                setTimeout(() => {
                    if (currentCycle < 3) {
                        currentCycle++;
                        displayImage = true;
                        startCycle();
                    } else {
                        showReturnButton = true;
                    }
                }, 30000);
            }, 30000);

            return () => {
                clearInterval(interval);
                if (browser) {
                    window.removeEventListener('keydown', handleKeyDown);
                    window.removeEventListener('keyup', handleKeyUp);
                }
            };
        };

        startCycle();
    });

    function handleKeyDown(event: KeyboardEvent) {
        if (!displayImage && ['ArrowDown', 'ArrowLeft', 'ArrowRight'].includes(event.key)) {
            keyPressStart[event.key] = keyPressStart[event.key] || performance.now();
        }
    }

    function handleKeyUp(event: KeyboardEvent) {
        if (!displayImage && keyPressStart[event.key]) {
            keyDownTimes[event.key] += performance.now() - keyPressStart[event.key]!;
            keyPressStart[event.key] = null;
            exportToCSV();
        }
    }

    function exportToCSV() {
        const csvContent = `data:text/csv;charset=utf-8,${Object.entries(keyDownTimes)
            .map(([key, duration]) => `${key},${duration}`)
            .join("\n")}`;
        const encodedUri = encodeURI(csvContent);
        const link = document.createElement("a");
        link.setAttribute("href", encodedUri);
        link.setAttribute("download", "key_press_times.csv");
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }

    function goToMainMenu() {
        goto('/'); // Reemplaza '/' con la ruta del menú principal
    }
</script>

<!-- Resto del HTML y estilos -->


<div class="center">
    {#if displayImage}
        <!-- svelte-ignore a11y-missing-attribute -->
        <img src="/images/exp1_adapt.png" class="image" style="transform: rotate({$rotation}deg);">
    {:else}
        <!-- svelte-ignore a11y-missing-attribute -->
        <img src="/images/exp1_test.png" class="image-static">
    {/if}

    {#if showReturnButton}
        <button on:click={goToMainMenu}>Return to Main Menu</button>
    {/if}
</div>

<style>
    .center {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh; /* Set full viewport height */
    }

    .image {
        width: 900px; /* Adjust size as needed */
        height: 900px; /* Adjust size as needed */
        object-fit: cover; /* Maintain aspect ratio of the image */
        transform-origin: center;
        transform: rotate(90deg);
    }

    .image-static {
        width: 900px; /* Adjust size as needed */
        height: 900px; /* Adjust size as needed */
        object-fit: cover; /* Maintain aspect ratio of the image */
    }
</style>
