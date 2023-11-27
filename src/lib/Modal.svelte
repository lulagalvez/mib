<script>
	import "./Modal.css";
	/**
	 * @type {any}
	 */
	export let showModal;

	/**
	 * @type {HTMLDialogElement}
	 */
	let dialog;
	/**
	 * @type {{ focus: () => void; }}
	 */
	let firstFocusableElement; // Reference to the first focusable element

	$: if (showModal && dialog) {
		dialog.showModal();
		setTimeout(() => {
			// Use setTimeout to ensure the DOM has updated
			firstFocusableElement.focus();
		}, 0);
	} else if (dialog) {
		dialog.close();
	}
	/**
	 * @type {() => void}
	 */
	export let closeModal;

	let userInputAlias = "";

	function handleOkClick() {
		if (userInputAlias.trim()) {
			localStorage.setItem("alias", userInputAlias);
			dialog.close();
			closeModal(); // Emit event to parent component
		} else {
			console.log("Please enter an alias");
		}
	}
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
<dialog bind:this={dialog} on:close>
	<!-- svelte-ignore a11y-no-static-element-interactions -->
	<div on:click|stopPropagation></div>
	<slot name="header" />
	<hr />
	<h2 bind:this={firstFocusableElement} tabindex="-1">
		<!-- svelte-ignore a11y-img-redundant-alt -->
		<div class="image-container">
			<img
				src="/images/welcome.png"
				class="static-image"
				alt="Welcome Image"
			/>
		</div>
	</h2>
	<hr />

	<!-- svelte-ignore a11y-missing-content -->
	<h1>
		Somos estudiantes de la Universidad de Concepción y hemos creado esta
		plataforma para probar dos fenómenos ópticos, la ceguera inducida por
		movimiento y el efecto residual de movimiento. ¿Pero, que son éstas
		ilusiones ópticas?
	</h1>

	<div class="container">
		<div class="column">
			<h2>
				El efecto residual de movimiento o <a
					href="https://en.wikipedia.org/wiki/Motion_aftereffect"
					target="_blank"
					rel="noopener noreferrer">MAE</a
				> sucede cuando miras fijamente por mucho tiempo algo que está en
				movimiento y luego apartas la mirada y te das cuenta de que lo que
				estás mirando parece estar moviéndose. Como cuando te quedas mirando
				una cascada y después de un rato todo se ve raro.
			</h2>
		</div>
		<div class="column">
			<!-- svelte-ignore a11y-missing-attribute -->
			<iframe
				src="https://giphy.com/embed/l3vQXKPdzpShJtTCE"
				width="480"
				height="270"
				frameBorder="0"
				class="giphy-embed"
				allowFullScreen
			></iframe>
		</div>
	</div>

	<div class="container">
		<div class="column">
			<h2>
				La ceguera inducida por movimiento o <a
					href="https://en.wikipedia.org/wiki/Motion-induced_blindness"
					target="_blank"
					rel="noopener noreferrer">MIB</a
				> por otro lado es una ilusión óptica que sucede cuando miras fijamente
				algo en movimiento que induce que dejes de percibir otras cosas,
				por ejemplo a la derecha si miras fijamente el centro, los puntos
				alrededor parecieran desaparecer de forma intermitente.
			</h2>
		</div>
		<div class="column">
			<!-- svelte-ignore a11y-missing-attribute -->
			<iframe
				src="https://giphy.com/embed/zDrZUSgLnpay7kiazh"
				width="480"
				height="270"
				frameBorder="0"
				class="giphy-embed"
				allowFullScreen
			></iframe>
		</div>
	</div>

	<h1>
		Nuestro objetivo en esta investigación es poder combinar las dos, es
		decir, queremos ver si es que podemos inducir ceguera, pero en vez de
		utilizar movimiento, vamos a usar la percepción de movimiento generada
		al mirar por mucho rato algo como una cascada.
	</h1>
	<hr />

	<div class="image-container">
		<img
			src="/images/comojugar.png"
			class="static-image"
			alt="How to play"
		/>
	</div>
	<hr />

	<h1>
		Primero te mostraremos un estimulo de adaptación por 30 segundos, donde
		tu vista se acostumbrará al movimiento, luego te mostraremos un estimulo
		de prueba estático por otros 30 segundos, este proceso se repetira tres
		veces, una por cada tipo de movimiento, sentido horario, antihorario y
		estático, el orden elegido al azar. Te invitamos a que apretes F11 para
		ponerte en pantalla completa y que acerces tu rostro al monitor.
	</h1>

	<div class="container">
		<div class="column">
			<!-- svelte-ignore a11y-missing-attribute -->
			<iframe
				src="https://giphy.com/embed/sBYDySrctteHM3HFQe"
				width="720"
				height="405"
				frameBorder="0"
				class="giphy-embed"
				allowFullScreen
			></iframe>
		</div>
		<div class="column">
			<!-- svelte-ignore a11y-missing-attribute -->
			<img
				src="/images/example.png"
				class="static-image"
				width="550"
				height="405"
				alt="How to play"
			/>
		</div>
	</div>

	<h1>
		Durante el periodo de prueba estático deberás registrar la desaparición
		que percibes de los tres puntos amarillos. Para ello deberás usar las
		flechas del teclado: la flecha hacia la izquierda para el punto de
		arriba a la izquierda, la flecha hacia abajo para el punto de abajo y la
		flecha hacia la derecha para el punto de arriba a la derecha.
	</h1>

	<br />
	<br />
	<br />

	<div class="container">
		<div class="column">
			<div class="image-container">
				<img
					src="/images/triangle.png"
					class="dots-image"
					alt="Three dots"
				/>
			</div>
		</div>
		<div class="column">
			<div class="image-container">
				<img src="/images/arrows.png" alt="Arrow Keys" />
			</div>
		</div>
	</div>

	<br />
	<br />
	<br />

	<h1>
		Cuando percibes que uno de los puntos desaparece totalmente, mantienes
		apretado la flecha correspondiente hasta que vuelvas a percibirlo,
		momento en el que la sueltas.
	</h1>

	<hr />
	<div class="image-container">
		<!-- svelte-ignore a11y-img-redundant-alt -->
		<img
			src="/images/consentimiento.png"
			class="static-image"
			alt="Informed Consent"
		/>
	</div>
	<hr />

	<h1>
		Ahora que entendiste como participar en nuestro estudio, deberás aceptar
		el siguiente contrato de consentimiento informado que explica en detalle
		lo que significa tu participación. Una vez que aceptes, ingresa un alias
		para identificarte y apreta OK. Muchas gracias!
	</h1>

	<h2>Título del Estudio:</h2>
	<p>Ceguera inducida por secuelas de movimiento</p>

	<h2>Investigador Principal:</h2>
	<p>Luis Galvez Paes</p>

	<h2>Institución:</h2>
	<p>Universidad de Concepción</p>

	<h2>Objetivo del Estudio:</h2>
	<p>
		Este estudio tiene como objetivo investigar la percepción visual de
		imágenes después de percibir movimiento generado computacionalmente.
	</p>

	<h2>Procedimiento:</h2>
	<p>
		Durante la participación en este estudio, se le pedirá que observe
		estímulos visuales generados por la computadora en su monitor. Se le
		solicitará que fije la vista en estímulos de adaptación rotativos o
		estáticos durante 30 segundos, seguidos de la observación de patrones de
		prueba durante otros 30 segundos. Se le pedirá que indique la
		desaparición de puntos objetivo presionando y manteniendo presionada una
		combinación de tres teclas y soltando la tecla cuando el punto objetivo
		vuelva a aparecer.
	</p>

	<h2>Confidencialidad:</h2>
	<p>
		Toda la información recopilada durante este estudio se manejará de
		manera confidencial. Su identidad será protegida y no se divulgará
		ninguna información personal en informes o publicaciones resultantes.
	</p>

	<h2>Duración del Estudio:</h2>
	<p>
		La participación en este estudio implicará bloques de tres "pruebas"
		cada uno, con una duración total de aproximadamente 1 minuto. Es decir,
		la totalidad del experimento será de tres minutos.
	</p>

	<h2>Consentimiento Voluntario:</h2>
	<p>
		Su participación en este estudio es completamente voluntaria. Puede
		retirarse en cualquier momento sin penalización. Si decide retirarse, la
		información recopilada hasta ese momento se eliminará, y no se utilizará
		en análisis posteriores.
	</p>

	<h2>Riesgos y Beneficios:</h2>
	<p>
		No se espera que este estudio cause daño o malestar significativo. Los
		posibles beneficios incluyen una mayor comprensión de la percepción
		visual y adaptación en experimentos de efectos de ceguera inducida por
		secuelas de movimiento.
	</p>

	<h2>Contacto:</h2>
	<p>
		Si tiene alguna pregunta o inquietud sobre el estudio, puede comunicarse
		con Luis Gálvez Paes en <a href="mailto:luisgalvezuk@gmail.com"
			>luisgalvezuk@gmail.com</a
		>.
	</p>

	<h2>Consentimiento:</h2>
	<p>
		He leído y comprendido la información proporcionada anteriormente. Al
		ingresar mi alias estoy participando en este estudio y doy mi
		consentimiento voluntario para ser parte de la investigación.
	</p>
	<hr />
	<div class="form-container">
		<input
			type="text"
			bind:value={userInputAlias}
			placeholder="Ingrese su alias"
		/>
		<button on:click={handleOkClick}>OK</button>
	</div>
</dialog>
