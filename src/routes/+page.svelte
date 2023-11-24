<script>
// @ts-nocheck

	import "./global.css";
	import Modal from "$lib/Modal.svelte";

	// @ts-ignore
	/**
	 * @type {HTMLButtonElement}
	 */
	let button; // HTMLButtonElement
	let showModal = true;
</script>

<div class="content-container">

	<Modal
		{showModal}
		on:close={() => {
			showModal = false;
			button.focus();
		}}
	/>
		

	<h1>MOTION AFTER EFFECT INDUCED BLINDNESS</h1>

	<div class="triangle-container">
		<!-- svelte-ignore a11y-missing-content -->
		<a href="/exp1" class="circle circle1"></a>
		<!-- svelte-ignore a11y-missing-content -->
		<a href="/exp2" class="circle circle2"></a>
		<!-- svelte-ignore a11y-missing-content -->
		<a href="/exp3" class="circle circle3"></a>
	</div>

	<div>
		last edited by <a href="https://github.com/lulagalvez">Lula</a>
	</div>
</div>
