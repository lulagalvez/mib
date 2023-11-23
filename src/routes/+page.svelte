<script>
	import "./global.css";
	import Modal from '$lib/Modal.svelte';

	// @ts-ignore
	/**
     * @type {HTMLButtonElement}
     */
	let button; // HTMLButtonElement
	let showModal = false;
</script>

<div class="content-container">
	<button bind:this={button} on:click={() => (showModal = true)}>
		show modal
	</button>
	
	<Modal
		{showModal}
		on:close={() => {
			showModal = false;
			button.focus();
		}}
	>
		<h2 slot="header">
			modal
			<small><em>adjective</em> mod·al \ˈmō-dəl\</small>
		</h2>
	
		<ol class="definition-list">
			<li>of or relating to modality in logic</li>
			<li>
				containing provisions as to the mode of procedure or the manner of taking effect —used of a contract or legacy
			</li>
			<li>of or relating to a musical mode</li>
			<li>of or relating to structure as opposed to substance</li>
			<li>of, relating to, or constituting a grammatical form or category characteristically indicating predication</li>
			<li>of or relating to a statistical mode</li>
		</ol>
	
		<a href="https://www.merriam-webster.com/dictionary/modal">merriam-webster.com</a>
	</Modal>

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
