<script lang="ts">
    import "../global.css";// Importa estilos globales
    import { onMount, onDestroy } from "svelte";// Importa métodos de ciclo de vida de Svelte
    import { tweened } from "svelte/motion";// Importa para animaciones
    import { cubicOut } from "svelte/easing";// Importa para definir la curva de animación
    import { browser } from "$app/environment";// Importa para detectar el entorno del navegador
    import { goto } from "$app/navigation";// Importa para la navegación dentro de la aplicación
    
    // Animación de rotación con duración de 3000ms
    const rotation = tweened(0, {
        duration: 3000,
    });

    // Variables de estado del experimento
    let countdown = 3;  // Cuenta regresiva antes de iniciar el experimento
    const intervalDuration = 30000;// Duración de cada intervalo en el experimento
    let userAlias = ""; // // Alias del usuario participante en el experimento
    let displayImage = true;// Controla si la imagen debe mostrarse o no
    let currentCycle = 1; // Número actual del ciclo dentro del experimento
    let showReturnButton = false; // Controla la visibilidad del botón de retorno al menú principal


    // Estructura de datos para almacenar información de cada ciclo
    interface CycleData {
        alias: string;
        experiment: string;
        movementType: string; // Tipo de movimiento en el ciclo actual
        ArrowDown: number;// Tiempo presionando la flecha hacia abajo
        ArrowLeft: number;// Tiempo presionando la flecha hacia izquierda
        ArrowRight: number;// Tiempo presionando la flecha hacia derecha
        total: number;// Tiempo total presionando teclas
        dateTime: string;// Fecha y hora del registro
    }
    let cycleTimes: CycleData[] = [];// Arreglo para almacenar los datos de cada ciclo

     // Registro de tiempos de teclas presionadas
    let keyDownTimes: { [key: string]: number } = {
        ArrowDown: 0,
        ArrowLeft: 0,
        ArrowRight: 0,
    };
    let keyPressStart: { [key: string]: number | null } = {};// Momento en que se presiona una tecla

     // Arreglo de movimientos posibles en el experimento ->  0: sin movimiento, 1: en sentido horario, -1: en sentido antihorario
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
        // intervalo para actualizar la animación de rotación
        const interval = setInterval(() => {
            rotation.update((n) => n + 360 * currentMovement);
        }, 3000);
        // finalizar el intervalo y pasar al siguiente estado
        setTimeout(() => {
            clearInterval(interval);// Detiene la animación de rotación
            displayImage = false;   // Oculta la imagen

            // Inicia escucha de eventos de teclado solo si es necesario
            if (browser && currentCycle <= 3) {
                window.addEventListener("keydown", handleKeyDown);
                window.addEventListener("keyup", handleKeyUp);
            }

            // Espera 30 segundos más para cambiar de imagen o terminar el ciclo
            setTimeout(() => {
                if (currentCycle < 3) {
                    saveAndExportCycleData(); // Guarda los datos del ciclo actual
                    currentCycle++; // Incrementa el número de ciclo
                    displayImage = true; // Muestra la imagen nuevamente
                    startCycle(); // Inicia el siguiente ciclo
                } else {
                    saveAndExportCycleData(); // Guarda los datos del último ciclo
                    showReturnButton = true; // Muestra el botón para retornar al menú
                }
            }, intervalDuration);
        }, intervalDuration);
        // Función de limpieza para el efecto
        return () => {
            clearInterval(interval);// Limpia el intervalo de rotación
            if (browser) {
                window.removeEventListener("keydown", handleKeyDown);
                window.removeEventListener("keyup", handleKeyUp);
            }
        };
    };

    let currentMovement = 0; // Variable para almacenar el tipo de movimiento actual
    onMount(() => {
        // Crea un intervalo para la cuenta regresiva antes de empezar el experimento
        const countdownInterval = setInterval(() => {
            countdown -= 1;
            if (countdown === 0) {
                clearInterval(countdownInterval);
            }
        }, 1000);
        // Obtiene el alias del usuario del almacenamiento local
        userAlias = localStorage.getItem("alias") || "Unknown";

        startCycle();
    });
    // Función asíncrona para enviar datos al servicio de Google Sheets a través de una API.
    async function postToSheetAPI() {
        try {
            // Realiza una solicitud HTTP POST a la URL de la API proporcionada por Sheet.best,
            // que actúa como intermediario entre la aplicación y Google Sheets.

            const response = await fetch(
                "https://sheet.best/api/sheets/81947b65-2fbd-4577-8ab8-583c4ce45b2f",
                {
                    method: "POST", 
                    headers: {
                        "Content-Type": "application/json", 
                    },
                    // El cuerpo de la solicitud es una cadena JSON que contiene los datos de cycleTimes.
                    // Estos datos serán insertados en las filas correspondientes de la hoja de cálculo de Google.
                    body: JSON.stringify(cycleTimes),
                },
            );

            if (!response.ok) {
                // En caso de una respuesta no exitosa, lanza un error con el estado HTTP.
                // Esto puede indicar un problema con la conexión a la API o con la hoja de cálculo.

                throw new Error(`HTTP error! status: ${response.status}`);
            }
            // Si la respuesta es exitosa, convierte la respuesta a formato JSON.
            const result = await response.json();
             // Registra en la consola el éxito de la operación, mostrando la respuesta del servidor.
            console.log("Data posted successfully:", result);
        } catch (error) {
            // Captura y registra cualquier error durante la solicitud o en el procesamiento de la respuesta.
            // Esto incluye errores de red, errores de API, o problemas con la estructura de los datos enviados.
            console.error("Error posting data:", error);
        }
    }
    // guardar y preparar los datos de cada ciclo del experimento para su exportación.
    function saveAndExportCycleData() {
        // Calcula el tiempo total de presión de teclas, sumando los tiempos de cada tecla.
        const total = parseFloat(
            Object.values(keyDownTimes)
                .reduce((a, b) => a + b, 0)
                .toFixed(3),
        );
        // Obtiene la fecha y hora actual en formato ISO para registrar cuando se completó el ciclo.
        const dateTime = new Date().toISOString();

        // Determina el tipo de movimiento realizado en el ciclo actual.
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
        // Añade los datos del ciclo actual al arreglo 'cycleTimes'.
        // Esto incluye alias del usuario, tipo de movimiento, datos de teclas presionadas, tiempo total y fecha/hora.
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
        // Si es el último ciclo, llama a la función para enviar los datos a Google Sheets.
        if (currentCycle === 3) {
            postToSheetAPI();
        }
    }
    // manejar el evento de presión de teclas.
    function handleKeyDown(event: KeyboardEvent) {
        // Verifica si la imagen no se está mostrando y si la tecla presionada es una de las teclas de interés.
        if (
            !displayImage &&
            ["ArrowDown", "ArrowLeft", "ArrowRight"].includes(event.key)
        ) {
            // Registra el momento en que se empezó a presionar una tecla,
            // si es la primera vez que se presiona en el ciclo actual.
            keyPressStart[event.key] =
                keyPressStart[event.key] || performance.now();
        }
    }
    // manejar el evento de liberación de teclas.
    function handleKeyUp(event: KeyboardEvent) {
        if (!displayImage && keyPressStart[event.key]) {
            // Redondea a 3 decimales y convierte a número
            keyDownTimes[event.key] += parseFloat(
                (
                    (performance.now() - keyPressStart[event.key]!) /
                    1000
                ).toFixed(3),
            );
            // Reinicia el contador de inicio de presión para esa tecla.
            keyPressStart[event.key] = null;
        }
    }
    // Función para reiniciar el experimento y volver a su estado inicial.
    function resetExperiment() {
        currentCycle = 1;
        showReturnButton = false;
        displayImage = true;
        startCycle();
    }
    // Función para navegar al menú principal de la aplicación.
    function goToMainMenu() {
        // Utiliza la función 'goto' de SvelteKit para cambiar la ruta.
        // Aquí, '/' representa la ruta del menú principal.
        // Cambia esto según la estructura de rutas de tu aplicación.
        goto("/"); // Reemplaza '/' con la ruta del menú principal
    }
</script>

<!-- Resto del HTML y estilos -->
<!-- Componente visual principal que muestra diferentes elementos basados en el estado del experimento -->
<div class="center">
    {#if countdown > 0}
    <!-- Muestra una cuenta regresiva antes de comenzar el experimento -->
        <div class="countdown">{countdown}</div>
    {:else if displayImage}
        <!-- Muestra la imagen con animación de rotación cuando 'displayImage' es verdadero -->
        <!-- svelte-ignore a11y-missing-attribute -->
        <img
            src="/images/exp1_adapt.png"
            class="image"
            style="transform: rotate({$rotation}deg);"
        />
    {:else if showReturnButton}
        <!-- Se muestra al finalizar el experimento, con opciones para volver al menú o reiniciar -->
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
        <!-- Muestra una imagen estática cuando no hay cuenta regresiva o animación -->
        <!-- svelte-ignore a11y-missing-attribute -->
        <img src="/images/exp1_test.png" class="image-static" />
    {/if}
</div>

<style>
    .center {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 90vh;
        overflow-y: scroll;
        scrollbar-width: none; /* Firefox */
        -ms-overflow-style: none; /* Firefox */ /* Set full viewport height */
    }
    .center::-webkit-scrollbar {
        display: none; /* Safari and Chrome */
    }
   
     /* Estilos para la imagen con animación de rotación */
    .image {
        width: 636px; /* Adjust size as needed */
        height: 636px; /* Adjust size as needed */
        object-fit: cover; /* Maintain aspect ratio of the image */
        transform-origin: center;
        transform: rotate(90deg);
    }

    /* Estilos para la imagen estática */
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
    /* Estilos para la cuenta regresiva */
    .countdown {
        font-size: 5em; /* Large font size for countdown */
        color: white; /* Color of the countdown numbers */
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    button {
        width: 50%;
        /* Adjust the width as needed */
        padding: 15px;
        /* Adds space inside the button */
        font-size: 1.2em;
        /* Increase the font size */
        color: white;
        /* Text color */
        background-color: #101010;
        /* Background color */
        border: none;
        /* No border */
        border-radius: 5px;
        /* Rounded corners */
        cursor: pointer;
        /* Cursor changes to pointer on hover */
        margin: 10px 0;
        /* Adds space around the button */
        border: 2px solid white;
        /* White border */
        border-radius: 5px;
        /* Rounded corners */
        cursor: pointer;
        /* Cursor changes to pointer on hover */
        margin: 10px 0;
        /* Adds space around the button */
    }

    button:hover {
        background-color: white;
        /* Darker background color on hover */
        color: #101010;
    }
</style>
