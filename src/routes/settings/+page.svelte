<script>
    import { onMount } from 'svelte';
    import "../global.css";
    import "./settings.css";


    function clearLocalStorage() {
        if (confirm("¿Estás seguro de que deseas limpiar la memoria?")) {
            localStorage.clear();
            // Additional actions after clearing storage
        }
    }
</script>


<div class="settings-container">
    <h1>Configuración</h1>

    <div class="settings-container">
        <!-- Other settings components -->


        <button on:click={clearLocalStorage}>Limpiar memoria local</button>
    </div>
</div>
