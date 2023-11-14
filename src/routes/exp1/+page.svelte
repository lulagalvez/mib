<script>
    import "../global.css"
    import { onMount } from "svelte";
    import { tweened } from "svelte/motion";
    import { cubicOut } from "svelte/easing";

    const rotation = tweened(0, {
        duration: 3000, // Adjust duration as needed
    });

    let displayImage = true;

    onMount(() => {
        // Automatically rotate by 360 degrees every 3 seconds (3000 milliseconds) for 10 iterations
        const interval = setInterval(() => {
            rotation.set($rotation + 360);
        }, 3000);

        setTimeout(() => {
            clearInterval(interval);
            displayImage = false; // After 30 seconds, stop animation and display something else
        }, 30000); // Stop after 30 seconds

        return () => clearInterval(interval);
    });
</script>

<div class="center">
    {#if displayImage}
        <!-- svelte-ignore a11y-missing-attribute -->
        <img src="/images/exp1_adapt.png" class="image" style="transform: rotate({$rotation}deg);">
    {:else}
        <!-- svelte-ignore a11y-missing-attribute -->
        <img src="/images/exp1_test.png" class="image-static">
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
        transform: rotate(45deg);
    }

    .image-static {
        width: 900px; /* Adjust size as needed */
        height: 900px; /* Adjust size as needed */
        object-fit: cover; /* Maintain aspect ratio of the image */
    }
</style>
