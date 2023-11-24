<script>
  import "../global.css";
  import "./about.css";
</script>

<div class="about-container">
  <!-- svelte-ignore a11y-missing-content -->
  <h1>
    Somos estudiantes de la Universidad de Concepción y hemos creado esta
    plataforma para probar dos fenomenos ópticos, la ceguera inducida por
    movimiento y el efecto residual de movimiento. Pero, que son éstas ilusiones
    ópticas?
  </h1>

  <div class="container">
    <div class="column">
      <h2>
        El efecto residual de movimiento o <a
          href="https://en.wikipedia.org/wiki/Motion_aftereffect"
          target="_blank"
          rel="noopener noreferrer">MAE</a
        > sucede cuando miras fijamente por mucho tiempo algo que está en movimiento
        y luego apartas la mirada y te das cuenta de que lo que estás mirando parece
        estar moviéndose. Como cuando te quedas mirando una cascada y después de
        un rato todo se ve raro.
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
        La cegueda inducida por movimiento o <a
          href="https://en.wikipedia.org/wiki/Motion-induced_blindness"
          target="_blank"
          rel="noopener noreferrer">MIB</a
        > por otro lado es una ilusión óptica que sucede cuando miras fijamente algo
        en movimiento que induce que dejes de percibir otras cosas, por ejemplo a
        la derecha si miras fijamente el centro, los puntos alrededor parecieran
        desaparecer de forma intermitente.
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
    Nuestro objetivo en esta investigación es poder combinar las dos, es decir,
    queremos ver si es que podemos inducir ceguera, pero en vez de utilizar
    movimiento, vamos a usar la percepción de movimiento generada al mirar por
    mucho rato algo como una cascada.
  </h1>

  <div class="image-container">
    <img src="/images/comojugar.png" class="static-image" alt="How to play" />
  </div>

  <h1>
    Primero te mostraremos un estimulo de adaptación por 30 segundos, donde tu
    vista se acostumbrará al movimiento, luego te mostraremos un estimulo de
    prueba estático.
  </h1>

  <div class="container">
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
    Durante el periodo de prueba estático deberás registrar la desaparición que
    percibes de los tres puntos amarillos. Para ello deberás usar las flechas
    del teclado: la flecha hacia la izquierda para el punto de arriba a la
    izquierda, la flecha hacia abajo para el punto de abajo y la flecha hacia la
    derecha para el punto de arriba a la derecha.
  </h1>

  <br />
  <br />
  <br />

  <div class="container">
    <div class="column">
      <div class="image-container">
        <img src="/images/triangle.png" class="dots-image" alt="Three dots" />
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
    apretado la flecha correspondiente hasta que vuelvas a percibirlo, momento
    en el que la sueltas.
  </h1>

  <hr />
</div>
