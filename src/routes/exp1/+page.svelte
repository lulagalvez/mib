<script lang="ts">
    import "../global.css";
    import { onMount, onDestroy } from "svelte";
    import { tweened } from "svelte/motion";
    import { cubicOut } from "svelte/easing";
    import { browser } from "$app/environment";
    import { goto } from "$app/navigation";

    const rotation = tweened(0, {
        duration: 3000,
    });

    let countdown = 3;
    const intervalDuration = 30000;
    let userAlias = ""; // Variable to store the alias
    let displayImage = true;
    let currentCycle = 1; // Inicia en el primer ciclo
    let showReturnButton = false; // Controla la visibilidad del botón de retorno
    interface CycleData {
        alias: string;
        experiment: string;
        movementType: string; // Nueva propiedad
        ArrowDown: number;
        ArrowLeft: number;
        ArrowRight: number;
        total: number;
        dateTime: string;
    }
    let cycleTimes: CycleData[] = [];
    let keyDownTimes: { [key: string]: number } = {
        ArrowDown: 0,
        ArrowLeft: 0,
        ArrowRight: 0,
    };
    let keyPressStart: { [key: string]: number | null } = {};

    const movements = [0, 1, -1];

    // Función para mezclar el arreglo
    function shuffle(array: number[]): number[] {
        for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
        }
        return array;
    }

    // Mezclar los movimientos
    const shuffledMovements = shuffle([...movements]);

    const startCycle = () => {
        // Actualiza el tipo de movimiento al inicio de cada ciclo
        currentMovement = shuffledMovements[currentCycle - 1];
        // Restablece las variables de tiempo
        keyDownTimes = { ArrowDown: 0, ArrowLeft: 0, ArrowRight: 0 };
        keyPressStart = {};
        // Intervalo para rotación
        const interval = setInterval(() => {
            rotation.update((n) => n + 360 * currentMovement);
        }, 3000);

        setTimeout(() => {
            clearInterval(interval);
            displayImage = false;

            // Inicia escucha de eventos de teclado solo si es necesario
            if (browser && currentCycle <= 3) {
                window.addEventListener("keydown", handleKeyDown);
                window.addEventListener("keyup", handleKeyUp);
            }

            // Espera 30 segundos más para cambiar de imagen o terminar el ciclo
            setTimeout(() => {
                if (currentCycle < 3) {
                    saveAndExportCycleData();
                    currentCycle++;
                    displayImage = true;
                    startCycle();
                } else {
                    saveAndExportCycleData();
                    showReturnButton = true;
                }
            }, intervalDuration);
        }, intervalDuration);

        return () => {
            clearInterval(interval);
            if (browser) {
                window.removeEventListener("keydown", handleKeyDown);
                window.removeEventListener("keyup", handleKeyUp);
            }
        };
    };

    let currentMovement = 0; // Variable para almacenar el tipo de movimiento actual
    onMount(() => {
        const countdownInterval = setInterval(() => {
            countdown -= 1;
            if (countdown === 0) {
                clearInterval(countdownInterval);
            }
        }, 1000);
        userAlias = localStorage.getItem("alias") || "Unknown";

        startCycle();
    });

    async function postToSheetAPI() {
        try {
            const response = await fetch(
                "https://sheet.best/api/sheets/81947b65-2fbd-4577-8ab8-583c4ce45b2f",
                {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify(cycleTimes),
                },
            );

            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }

            const result = await response.json();
            console.log("Data posted successfully:", result);
        } catch (error) {
            console.error("Error posting data:", error);
        }
    }

    function saveAndExportCycleData() {
        const total = parseFloat(
            Object.values(keyDownTimes)
                .reduce((a, b) => a + b, 0)
                .toFixed(3),
        );
        const dateTime = new Date().toISOString();

        let movementType = "";
        switch (currentMovement) {
            case 1:
                movementType = "Horario";
                break;
            case -1:
                movementType = "Antihorario";
                break;
            default:
                movementType = "Sin Girar";
        }

        cycleTimes.push({
            alias: userAlias,
            movementType,
            experiment: "exp1", // Agregado aquí
            ArrowLeft: keyDownTimes["ArrowLeft"],
            ArrowDown: keyDownTimes["ArrowDown"],
            ArrowRight: keyDownTimes["ArrowRight"],
            total,
            dateTime,
        });

        if (currentCycle === 3) {
            postToSheetAPI();
        }
    }

    function handleKeyDown(event: KeyboardEvent) {
        if (
            !displayImage &&
            ["ArrowDown", "ArrowLeft", "ArrowRight"].includes(event.key)
        ) {
            keyPressStart[event.key] =
                keyPressStart[event.key] || performance.now();
        }
    }

    function handleKeyUp(event: KeyboardEvent) {
        if (!displayImage && keyPressStart[event.key]) {
            // Redondea a 3 decimales y convierte a número
            keyDownTimes[event.key] += parseFloat(
                (
                    (performance.now() - keyPressStart[event.key]!) /
                    1000
                ).toFixed(3),
            );
            keyPressStart[event.key] = null;
        }
    }

    function resetExperiment() {
        currentCycle = 1;
        showReturnButton = false;
        displayImage = true;
        startCycle();
    }

    function goToMainMenu() {
        goto("/"); // Reemplaza '/' con la ruta del menú principal
    }
</script>

<!-- Resto del HTML y estilos -->

<div class="center">
    {#if countdown > 0}
        <div class="countdown">{countdown}</div>
    {:else if displayImage}
        <!-- svelte-ignore a11y-missing-attribute -->
        <img
            src="/images/exp1_adapt.png"
            class="image"
            style="transform: rotate({$rotation}deg);"
        />
    {:else if showReturnButton}
        <!-- svelte-ignore a11y-missing-attribute -->
        <img src="/images/listo.png" />

        <h1>
            ¡Felicidades! Has terminado de realizar el experimento 1 y los datos
            de tus respuestas han sido guardados. Muchas gracias por tu
            cooperación. Te invitamos a que pruebes los otros dos experimentos.
        </h1>

        <div class="button-container">
            <button on:click={goToMainMenu}>Volver al menú principal</button>
            <button on:click={resetExperiment}>Jugar de nuevo</button>
        </div>
    {:else}
        <!-- svelte-ignore a11y-missing-attribute -->
        <img src="/images/exp1_test.png" class="image-static" />
    {/if}
</div>

<style>
    .center {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 90vh; /* Set full viewport height */
    }

    .image {
        width: 636px; /* Adjust size as needed */
        height: 636px; /* Adjust size as needed */
        object-fit: cover; /* Maintain aspect ratio of the image */
        transform-origin: center;
        transform: rotate(90deg);
    }

    .image-static {
        width: 636px; /* Adjust size as needed */
        height: 636px; /* Adjust size as needed */
        object-fit: cover; /* Maintain aspect ratio of the image */
    }

    .center {
        display: flex;
        flex-direction: column; /* Organizes children vertically */
        justify-content: center;
        align-items: center;
        height: 90vh;
    }

    .button-container {
        display: flex; /* Flex container for the buttons */
        justify-content: space-between; /* Space between the buttons */
        padding-top: 20px; /* Padding above the buttons */
    }

    .button-container button {
        margin: 0 10px; /* Spacing between buttons */
    }
    .countdown {
        font-size: 5em; /* Large font size for countdown */
        color: white; /* Color of the countdown numbers */
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }
</style>
