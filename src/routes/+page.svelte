<script>
	// @ts-nocheck
	import { onMount } from "svelte";
	import "./global.css";
	import Modal from "$lib/Modal.svelte";

	// @ts-ignore
	/**
	 * @type {HTMLButtonElement}
	 */
	let button; // HTMLButtonElement
	let showModal = true;
	let alias;

	onMount(() => {
		alias = localStorage.getItem("alias"); // Retrieve alias from localStorage
		if(alias) {
			showModal = false;
			console.log(alias)
		}
		console.log(showModal)
	});

	function handleCloseModal() {
		showModal = false;
        if (button) {
            button.focus();
        }
	}
</script>

<div class="content-container">
	<Modal
		{showModal}
		on:close={handleCloseModal}
	/>

	<!-- svelte-ignore a11y-img-redundant-alt -->
	<img src="/images/title.png" class="title-image" alt="Title" />

	<div class="triangle-container">
		<!-- svelte-ignore a11y-missing-content -->
		<a href="/exp1" class="circle circle1"></a>
		<!-- svelte-ignore a11y-missing-content -->
		<a href="/exp2" class="circle circle2"></a>
		<!-- svelte-ignore a11y-missing-content -->
		<a href="/exp3" class="circle circle3"></a>
	</div>

	<div class="centered-text">
		<h3>
			Cada circulo corresponde a un experimento distinto. ¡Haz click en alguno para comenzar!
	</div>

	<footer class="footer">
		<p class="footer-text">Creado por estudiantes de<br>la Universidad de Concepción</p>
		<img src="/images/logo.png" alt="Logo" class="footer-logo">
	</footer>

	
</div>
