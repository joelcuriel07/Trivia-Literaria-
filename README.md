# Trivia-Literaria-
Juego de preguntas de literatura e historia por turno para 2 jugadores 
html_content = """
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Trivia Literaria</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f0f8;
      text-align: center;
      padding: 20px;
    }
    .trivia-box {
      background: white;
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 0 10px #ccc;
      max-width: 700px;
      margin: auto;
    }
    button {
      margin: 10px;
      padding: 12px 24px;
      font-size: 16px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    }
    .opcion {
      background-color: #3498db;
      color: white;
      width: 80%;
    }
    .correcta {
      background-color: #2ecc71 !important;
    }
    .incorrecta {
      background-color: #e74c3c !important;
    }
    #temporizador {
      font-size: 20px;
      margin: 10px;
      color: #e67e22;
    }
    .jugador-activo {
      font-weight: bold;
      color: #8e44ad;
    }
  </style>
</head>
<body>
  <div class="trivia-box">
    <h1>Trivia Literaria</h1>
    <p id="jugador">Turno de: Jugador 1</p>
    <h2 id="pregunta">Cargando pregunta...</h2>
    <div id="opciones"></div>
    <p id="resultado"></p>
    <p id="temporizador">Tiempo: 20</p>
    <button onclick="siguientePregunta()">Siguiente</button>
    <p id="puntajes"></p>
    <button onclick="reiniciarJuego()" style="display:none" id="reiniciar">Reiniciar juego</button>
  </div>

  <script>
    const trivias = [
      {
        categoria: "Literatura",
        pregunta: "¿Quién escribió 'Don Quijote de la Mancha'?",
        opciones: ["Lope de Vega", "Miguel de Cervantes", "Federico García Lorca", "Pablo Neruda"],
        respuesta: "Miguel de Cervantes"
      },
      {
        categoria: "Historia",
        pregunta: "¿En qué año cayó el Imperio Romano de Occidente?",
        opciones: ["476 d.C.", "1492", "1066", "1810"],
        respuesta: "476 d.C."
      },
      {
        categoria: "Tecnología",
        pregunta: "¿Quién fundó Microsoft?",
        opciones: ["Steve Jobs", "Bill Gates", "Elon Musk", "Mark Zuckerberg"],
        respuesta: "Bill Gates"
      },
      {
        categoria: "Literatura",
        pregunta: "¿Cuál es la obra más famosa de Gabriel García Márquez?",
        opciones: ["La ciudad y los perros", "El amor en los tiempos del cólera", "Cien años de soledad", "Rayuela"],
        respuesta: "Cien años de soledad"
      },
      {
        categoria: "Historia",
        pregunta: "¿Qué revolución inició en 1789?",
        opciones: ["Revolución Rusa", "Revolución Francesa", "Revolución Industrial", "Revolución Mexicana"],
        respuesta: "Revolución Francesa"
      },
      {
        categoria: "Tecnología",
        pregunta: "¿Qué lenguaje se usa principalmente para crear páginas web?",
        opciones: ["Python", "C++", "HTML", "Java"],
        respuesta: "HTML"
      }
    ];

    let indice = 0;
    let tiempo = 20;
    let temporizador;
    let jugadorActual = 1;
    let puntajes = {1: 0, 2: 0};

    function cargarPregunta() {
      clearInterval(temporizador);
      tiempo = 20;
      actualizarTemporizador();
      temporizador = setInterval(contarTiempo, 1000);

      const trivia = trivias[indice];
      document.getElementById("pregunta").textContent = `${trivia.pregunta} (${trivia.categoria})`;
      document.getElementById("jugador").innerHTML = `Turno de: <span class="jugador-activo">Jugador ${jugadorActual}</span>`;
      const opcionesDiv = document.getElementById("opciones");
      opcionesDiv.innerHTML = "";
      document.getElementById("resultado").textContent = "";
      actualizarPuntajes();

      trivia.opciones.forEach(opcion => {
        const btn = document.createElement("button");
        btn.className = "opcion";
        btn.textContent = opcion;
        btn.onclick = () => verificarRespuesta(btn, opcion);
        opcionesDiv.appendChild(btn);
      });
    }

    function contarTiempo() {
      tiempo--;
      actualizarTemporizador();
      if (tiempo <= 0) {
        clearInterval(temporizador);
        bloquearOpciones();
        mostrarRespuestaCorrecta();
        document.getElementById("resultado").textContent = "¡Se acabó el tiempo!";
      }
    }

    function actualizarTemporizador() {
      document.getElementById("temporizador").textContent = `Tiempo: ${tiempo}`;
    }

    function verificarRespuesta(boton, opcionSeleccionada) {
      clearInterval(temporizador);
      const correcta = trivias[indice].respuesta;
      if (opcionSeleccionada === correcta) {
        boton.classList.add("correcta");
        document.getElementById("resultado").textContent = "¡Correcto!";
        puntajes[jugadorActual]++;
      } else {
        boton.classList.add("incorrecta");
        document.getElementById("resultado").textContent = `Incorrecto. La respuesta era: ${correcta}`;
        mostrarRespuestaCorrecta();
      }
      bloquearOpciones();
      actualizarPuntajes();
    }

    function mostrarRespuestaCorrecta() {
      const botones = document.querySelectorAll(".opcion");
      botones.forEach(boton => {
        if (boton.textContent === trivias[indice].respuesta) {
          boton.classList.add("correcta");
        }
      });
    }

    function bloquearOpciones() {
      document.querySelectorAll(".opcion").forEach(btn => btn.disabled = true);
    }

    function siguientePregunta() {
      jugadorActual = jugadorActual === 1 ? 2 : 1;
      if (indice < trivias.length - 1) {
        indice++;
        cargarPregunta();
      } else {
        document.getElementById("pregunta").textContent = "¡Juego terminado!";
        document.getElementById("opciones").innerHTML = "";
        document.getElementById("temporizador").style.display = "none";
        document.getElementById("resultado").textContent = `Puntaje final - Jugador 1: ${puntajes[1]} | Jugador 2: ${puntajes[2]}`;
        document.getElementById("reiniciar").style.display = "inline-block";
      }
    }

    function actualizarPuntajes() {
      document.getElementById("puntajes").textContent =
        `Puntaje - Jugador 1: ${puntajes[1]} | Jugador 2: ${puntajes[2]}`;
    }

    function reiniciarJuego() {
      indice = 0;
      puntajes = {1: 0, 2: 0};
      jugadorActual = 1;
      document.getElementById("reiniciar").style.display = "none";
      document.getElementById("temporizador").style.display = "block";
      cargarPregunta();
    }

    window.onload = cargarPregunta;
  </script>
</body>
</html>
"""

# Guardar el contenido en un archivo HTML
file_path = "/mnt/data/Trivia_Literaria.html"
with open(file_path, "w", encoding="utf-8") as f:
    f.write(html_content)

file_path
