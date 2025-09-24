<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Examen de Aprendizaje Artificial</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }

        .container {
            background: #fff;
            padding: 20px 40px;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            max-width: 600px;
            width: 100%;
            text-align: center;
        }

        header h1 {
            color: #444;
            margin-bottom: 20px;
        }

        #user-form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        #user-form input {
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 4px;
            font-size: 16px;
        }

        #user-form button, #download-score {
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }

        #user-form button:hover, #download-score:hover {
            background-color: #0056b3;
        }

        .hidden {
            display: none;
        }

        #question-area {
            text-align: left;
        }

        #options-container {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-top: 20px;
        }

        .option {
            padding: 12px;
            border: 1px solid #ccc;
            border-radius: 4px;
            cursor: pointer;
            transition: background-color 0.3s, border-color 0.3s;
        }

        .option:hover {
            background-color: #f0f0f0;
        }

        .option.correct {
            background-color: #28a745;
            color: white;
            border-color: #28a745;
        }

        .option.incorrect {
            background-color: #dc3545;
            color: white;
            border-color: #dc3545;
        }

        #timer {
            font-size: 18px;
            font-weight: bold;
            color: #555;
            margin-bottom: 10px;
        }

        .result-box {
            text-align: left;
            padding: 20px;
            border: 1px solid #ddd;
            border-radius: 8px;
            background-color: #f9f9f9;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Examen de Aprendizaje Artificial</h1>
        </header>
        <main id="main-content">
            <section id="user-info">
                <h2>Ingresa tus datos para comenzar</h2>
                <form id="user-form">
                    <label for="full-name">Nombre completo:</label>
                    <input type="text" id="full-name" name="full-name" required>
                    <label for="email">Correo electrónico:</label>
                    <input type="email" id="email" name="email" required>
                    <button type="submit">Iniciar Examen</button>
                </form>
            </section>
            
            <section id="quiz-container" class="hidden">
                <div id="question-area">
                    <p id="timer">Tiempo restante: 30s</p>
                    <h2 id="question-text"></h2>
                    <div id="options-container"></div>
                </div>
            </section>
            
            <section id="results-container" class="hidden">
                <div class="result-box">
                    <h2>Resultados del Examen</h2>
                    <p>Nombre: <span id="final-name"></span></p>
                    <p>Correo: <span id="final-email"></span></p>
                    <p>Puntuación: <span id="score"></span> / 10</p>
                    <button id="download-score">Descargar Puntuación</button>
                </div>
            </section>
        </main>
    </div>

    <script>
        // Array de preguntas y sus respuestas correctas
        const allQuestions = [
            { question: '¿Qué se entiende por aprendizaje artificial en el contexto de la inteligencia computacional?', options: ['Es la capacidad de un sistema para aprender de datos, identificar patrones y tomar decisiones con una mínima intervención humana.', 'Es el proceso de programar manualmente un sistema para resolver un problema complejo.', 'Es la simulación de procesos de pensamiento humano en una computadora.', 'Es la habilidad de un sistema para comunicarse en un lenguaje natural.'], correctAnswer: 'Es la capacidad de un sistema para aprender de datos, identificar patrones y tomar decisiones con una mínima intervención humana.' },
            { question: '¿Cuáles son las principales diferencias entre aprendizaje supervisado, no supervisado y por refuerzo?', options: ['El supervisado usa datos etiquetados, el no supervisado no los usa y el de refuerzo aprende a través de recompensas y castigos.', 'El supervisado es para clasificación, el no supervisado para regresión y el de refuerzo para agrupamiento.', 'El supervisado es el único que requiere datos, los otros dos no.', 'No existen diferencias, son términos intercambiables.'], correctAnswer: 'El supervisado usa datos etiquetados, el no supervisado no los usa y el de refuerzo aprende a través de recompensas y castigos.' },
            { question: '¿Qué papel juega el error en los modelos de aprendizaje artificial?', options: ['El error se ignora para evitar que el modelo se vuelva demasiado complejo.', 'El error es una medida de la precisión del modelo y se usa para guiar el proceso de entrenamiento, minimizando la discrepancia entre la salida del modelo y el valor real.', 'El error solo se usa en modelos de regresión.', 'El error indica si el modelo está sobreajustado.'], correctAnswer: 'El error es una medida de la precisión del modelo y se usa para guiar el proceso de entrenamiento, minimizando la discrepancia entre la salida del modelo y el valor real.' },
            { question: '¿Cómo se define la función de costo en un sistema de aprendizaje?', options: ['Es una métrica que indica el tiempo de ejecución del modelo.', 'Es una función que mide el desempeño del modelo en un conjunto de datos.', 'Es una función que mide la discrepancia entre la salida del modelo y el valor real, que el modelo intenta minimizar durante el entrenamiento.', 'Es la cantidad de recursos computacionales necesarios para entrenar el modelo.'], correctAnswer: 'Es una función que mide la discrepancia entre la salida del modelo y el valor real, que el modelo intenta minimizar durante el entrenamiento.' },
            { question: '¿Qué significa generalización en el aprendizaje artificial?', options: ['La capacidad de un modelo para predecir correctamente datos que ya ha visto durante el entrenamiento.', 'La capacidad de un modelo para predecir correctamente en datos nuevos y no vistos.', 'La capacidad de un modelo para trabajar en diferentes plataformas de hardware.', 'La capacidad de un modelo para generar nuevas preguntas y respuestas.'], correctAnswer: 'La capacidad de un modelo para predecir correctamente en datos nuevos y no vistos.' },
            { question: '¿Qué relación existe entre aprendizaje artificial y estadística?', options: ['El aprendizaje artificial se basa en la estadística para desarrollar algoritmos que aprenden de los datos.', 'Son campos completamente separados, sin relación alguna.', 'La estadística es un subcampo del aprendizaje artificial.', 'El aprendizaje artificial solo se ocupa de la clasificación, mientras que la estadística solo se ocupa de la regresión.'], correctAnswer: 'El aprendizaje artificial se basa en la estadística para desarrollar algoritmos que aprenden de los datos.' },
            { question: '¿Qué implicaciones éticas pueden surgir del uso de sistemas que aprenden automáticamente?', options: ['La falta de transparencia, el sesgo algorítmico y los riesgos para la privacidad y la seguridad de los datos.', 'Solo el riesgo de que el modelo no sea lo suficientemente preciso.', 'La dificultad de obtener datos para el entrenamiento.', 'Ninguna, ya que los sistemas que aprenden son totalmente objetivos.'], correctAnswer: 'La falta de transparencia, el sesgo algorítmico y los riesgos para la privacidad y la seguridad de los datos.' },
            { question: '¿Cómo se evalúa la eficiencia de un modelo de aprendizaje artificial?', options: ['Midiendo el tiempo de entrenamiento y la cantidad de datos que utiliza.', 'Usando métricas como la exactitud, la precisión, el recall y la curva ROC.', 'Evaluando el costo computacional del modelo.', 'No se puede evaluar la eficiencia, solo la precisión.'], correctAnswer: 'Usando métricas como la exactitud, la precisión, el recall y la curva ROC.' },
            { question: '¿Qué es el sobreajuste y cómo se puede evitar?', options: ['Es cuando un modelo es demasiado simple y no puede capturar los patrones en los datos. Se evita añadiendo más datos.', 'Es cuando un modelo aprende demasiado bien los datos de entrenamiento y no puede generalizar a datos nuevos. Se evita con técnicas como la validación cruzada y la regularización.', 'Es cuando un modelo tiene demasiadas neuronas. Se evita reduciendo el número de neuronas.', 'Es cuando el modelo tarda demasiado en entrenar. Se evita usando un hardware más potente.'], correctAnswer: 'Es cuando un modelo aprende demasiado bien los datos de entrenamiento y no puede generalizar a datos nuevos. Se evita con técnicas como la validación cruzada y la regularización.' },
            { question: '¿Qué diferencias hay entre aprendizaje simbólico y conexionista?', options: ['El simbólico se basa en reglas y lógica explícitas, mientras que el conexionista se basa en la interconexión de nodos (redes neuronales).', 'El simbólico solo se usa para el procesamiento del lenguaje natural, mientras que el conexionista se usa para imágenes.', 'El simbólico es un tipo de aprendizaje supervisado, y el conexionista es un tipo de aprendizaje no supervisado.', 'No hay diferencias, son términos que significan lo mismo.'], correctAnswer: 'El simbólico se basa en reglas y lógica explícitas, mientras que el conexionista se basa en la interconexión de nodos (redes neuronales).' },
            { question: '¿Cuáles son las partes principales de una neurona biológica?', options: ['Núcleo, membrana y mitocondria.', 'Axón, dendrita, soma y sinapsis.', 'Citoplasma, axón y neuritas.', 'Soma, dendritas y neurilema.'], correctAnswer: 'Axón, dendrita, soma y sinapsis.' },
            { question: '¿Qué función cumple la sinapsis en el sistema nervioso?', options: ['Almacena los recuerdos a largo plazo.', 'Es la estructura que permite la comunicación entre neuronas.', 'Produce energía para el funcionamiento de la neurona.', 'Procesa la información de entrada.'], correctAnswer: 'Es la estructura que permite la comunicación entre neuronas.' },
            { question: '¿Cómo se transmite la información en una neurona biológica?', options: ['Mediante un proceso puramente eléctrico a lo largo del axón.', 'Mediante un impulso electroquímico que se propaga desde el soma hasta las terminales del axón, cruzando la sinapsis mediante neurotransmisores.', 'A través de impulsos de luz que viajan por el axón.', 'Por medio de la vibración de las dendritas.'], correctAnswer: 'Mediante un impulso electroquímico que se propaga desde el soma hasta las terminales del axón, cruzando la sinapsis mediante neurotransmisores.' },
            { question: '¿Qué elementos componen una neurona artificial básica?', options: ['Unidad de entrada, unidad de salida y un procesador central.', 'Entradas, pesos, una función de suma y una función de activación.', 'Unidad de almacenamiento, una memoria y un procesador.', 'Un perceptrón y un perceptrón multicapa.'], correctAnswer: 'Entradas, pesos, una función de suma y una función de activación.' },
            { question: '¿Qué representa el peso sináptico en una neurona artificial?', options: ['La cantidad de datos de entrada.', 'La importancia o influencia de cada entrada en la salida de la neurona.', 'El número de neuronas conectadas.', 'El tiempo que tarda la neurona en procesar la información.'], correctAnswer: 'La importancia o influencia de cada entrada en la salida de la neurona.' },
            { question: '¿Cómo se calcula la salida de una neurona artificial?', options: ['Sumando todas las entradas y restando el peso.', 'Multiplicando cada entrada por su peso correspondiente, sumando todos los productos y aplicando una función de activación al resultado.', 'Sumando las entradas y los pesos.', 'Multiplicando todas las entradas por un solo peso.'], correctAnswer: 'Multiplicando cada entrada por su peso correspondiente, sumando todos los productos y aplicando una función de activación al resultado.' },
            { question: '¿Qué similitudes y diferencias hay entre la activación biológica y la función de activación artificial?', options: ['Ambas son idénticas, sin diferencias.', 'La activación biológica es un proceso de "todo o nada", mientras que la artificial puede ser continua. Ambas introducen no linealidad.', 'La biológica es continua y la artificial es discreta.', 'La biológica es un proceso físico, mientras que la artificial es solo una fórmula matemática.'], correctAnswer: 'La activación biológica es un proceso de "todo o nada", mientras que la artificial puede ser continua. Ambas introducen no linealidad.' },
            { question: '¿Por qué se considera la neurona artificial una simplificación del modelo biológico?', options: ['Porque no puede procesar información de la misma manera que una neurona biológica.', 'Porque solo considera los aspectos fundamentales de procesamiento de información, ignorando la complejidad química y morfológica de la neurona biológica.', 'Porque no puede funcionar sin un supervisor.', 'Porque no puede aprender de los datos.'], correctAnswer: 'Porque solo considera los aspectos fundamentales de procesamiento de información, ignorando la complejidad química y morfológica de la neurona biológica.' },
            { question: '¿Qué tipos de funciones de activación existen en redes neuronales?', options: ['Solo la función escalón.', 'Funciones lineales, sigmoidales, ReLU, tanh, entre otras.', 'Solo la función sigmoide.', 'Solo las funciones logarítmicas.'], correctAnswer: 'Funciones lineales, sigmoidales, ReLU, tanh, entre otras.' },
            { question: '¿Qué limitaciones tiene la analogía entre neuronas biológicas y artificiales?', options: ['La analogía es perfecta, no tiene limitaciones.', 'La neurona biológica es mucho más compleja, con plasticidad sináptica, múltiples neurotransmisores y un comportamiento dinámico que la artificial no replica.', 'La neurona biológica es un concepto obsoleto.', 'Las neuronas artificiales son más lentas que las biológicas.'], correctAnswer: 'La neurona biológica es mucho más compleja, con plasticidad sináptica, múltiples neurotransmisores y un comportamiento dinámico que la artificial no replica.' },
            { question: '¿Qué es un perceptrón y quién lo propuso originalmente?', options: ['Un tipo de neurona biológica, propuesto por Ramón y Cajal.', 'Un algoritmo de aprendizaje supervisado para clasificación, propuesto por Frank Rosenblatt.', 'Un tipo de red neuronal profunda, propuesto por Geoffrey Hinton.', 'Un modelo de lenguaje, propuesto por Alan Turing.'], correctAnswer: 'Un algoritmo de aprendizaje supervisado para clasificación, propuesto por Frank Rosenblatt.' },
            { question: '¿Qué tipo de problemas puede resolver un perceptrón simple?', options: ['Cualquier tipo de problema, sin limitaciones.', 'Problemas que son linealmente separables.', 'Problemas de clasificación de imágenes complejas.', 'Problemas de procesamiento de lenguaje natural.'], correctAnswer: 'Problemas que son linealmente separables.' },
            { question: '¿Cómo se actualizan los pesos en el algoritmo del perceptrón?', options: ['Se actualizan de forma aleatoria.', 'Se ajustan en cada iteración basándose en el error de predicción, sumando o restando una fracción del error a los pesos.', 'No se actualizan, son fijos.', 'Se actualizan solo al final del entrenamiento.'], correctAnswer: 'Se ajustan en cada iteración basándose en el error de predicción, sumando o restando una fracción del error a los pesos.' },
            { question: '¿Qué significa que un problema sea linealmente separable?', options: ['Que se puede resolver en una sola capa de la red neuronal.', 'Que los datos de las diferentes clases pueden ser divididos por una sola línea (en 2D) o un hiperplano (en más dimensiones).', 'Que los datos se distribuyen de manera uniforme en un plano.', 'Que no se puede resolver con un perceptrón.'], correctAnswer: 'Que los datos de las diferentes clases pueden ser divididos por una sola línea (en 2D) o un hiperplano (en más dimensiones).' },
            { question: '¿Qué limitaciones tiene el perceptrón simple?', options: ['No puede resolver problemas que no son linealmente separables, como el problema XOR.', 'Es demasiado lento para entrenar.', 'Solo puede usarse para regresión.', 'Solo funciona con datos de texto.'], correctAnswer: 'No puede resolver problemas que no son linealmente separables, como el problema XOR.' },
            { question: '¿Cómo se calcula la salida de un perceptrón?', options: ['Multiplicando las entradas por los pesos y aplicando una función de activación escalón.', 'Sumando las entradas.', 'Promediando las entradas y los pesos.', 'Multiplicando las entradas por un valor constante.'], correctAnswer: 'Multiplicando las entradas por los pesos y aplicando una función de activación escalón.' },
            { question: '¿Qué papel juega la función de activación en el perceptrón?', options: ['No tiene ningún papel.', 'Sirve para transformar la suma ponderada de las entradas en la salida final de la neurona, típicamente un valor binario (0 o 1) en el perceptrón simple.', 'Aumenta la velocidad de entrenamiento.', 'Solo se usa para los perceptrones multicapa.'], correctAnswer: 'Sirve para transformar la suma ponderada de las entradas en la salida final de la neurona, típicamente un valor binario (0 o 1) en el perceptrón simple.' },
            { question: '¿Cómo se entrena un perceptrón con un conjunto de datos?', options: ['De forma no supervisada, buscando patrones en los datos.', 'De forma supervisada, comparando la salida predicha con la etiqueta real y ajustando los pesos según el error.', 'Generando nuevas preguntas y respuestas.', 'Entrenando sin un supervisor y ajustando los pesos de forma aleatoria.'], correctAnswer: 'De forma supervisada, comparando la salida predicha con la etiqueta real y ajustando los pesos según el error.' },
            { question: '¿Qué ocurre si el perceptrón no converge durante el entrenamiento?', options: ['Significa que el problema no es linealmente separable y el algoritmo no encontrará una solución óptima.', 'Significa que el modelo es demasiado complejo.', 'Significa que el número de iteraciones es muy bajo.', 'Significa que el modelo está sobreajustado.'], correctAnswer: 'Significa que el problema no es linealmente separable y el algoritmo no encontrará una solución óptima.' },
            { question: '¿Qué mejoras introducen los perceptrones multicapa?', options: ['La capacidad de resolver problemas no linealmente separables, como el problema XOR.', 'La capacidad de usar solo una función de activación lineal.', 'La capacidad de usar menos datos de entrenamiento.', 'La capacidad de entrenar más rápido.'], correctAnswer: 'La capacidad de resolver problemas no linealmente separables, como el problema XOR.' },
            { question: '¿Qué significa ADALINE y quién lo desarrolló?', options: ['"Adaptive Linear Neuron", desarrollado por Bernard Widrow y Ted Hoff.', '"Adaptive Deep Learning Neuron", desarrollado por Yann LeCun.', '"Artificial Data Line", desarrollado por Alex Graves.', '"Advanced Data Integration Neuron", desarrollado por Yoshua Bengio.'], correctAnswer: '"Adaptive Linear Neuron", desarrollado por Bernard Widrow y Ted Hoff.' },
            { question: '¿En qué se diferencia ADALINE del perceptrón en cuanto a la función de activación?', options: ['ADALINE usa una función de activación sigmoide, mientras que el perceptrón usa una función escalón.', 'ADALINE usa una función de activación lineal en la capa de salida para calcular la salida, mientras que el perceptrón usa una función escalón para la clasificación.', 'No hay diferencia, ambos usan la misma función de activación.', 'ADALINE no usa una función de activación.'], correctAnswer: 'ADALINE usa una función de activación lineal en la capa de salida para calcular la salida, mientras que el perceptrón usa una función escalón para la clasificación.' },
            { question: '¿Qué tipo de función de error utiliza ADALINE?', options: ['Error absoluto.', 'Error logarítmico.', 'Error cuadrático medio (MSE).', 'Ninguna de las anteriores, no utiliza función de error.'], correctAnswer: 'Error cuadrático medio (MSE).' },
            { question: '¿Cómo se actualizan los pesos en ADALINE?', options: ['Se actualizan solo si hay un error de clasificación.', 'Se ajustan en cada iteración usando la regla de gradiente descendiente, basada en el error cuadrático medio.', 'Se actualizan aleatoriamente.', 'No se actualizan, son fijos.'], correctAnswer: 'Se ajustan en cada iteración usando la regla de gradiente descendiente, basada en el error cuadrático medio.' },
            { question: '¿Qué ventajas ofrece ADALINE frente al perceptrón en problemas de regresión?', options: ['Ninguna, ADALINE solo se usa para clasificación.', 'ADALINE puede resolver problemas de regresión lineal, mientras que el perceptrón no puede.', 'Es más rápido que el perceptrón.', 'ADALINE puede resolver problemas no lineales.'], correctAnswer: 'ADALINE puede resolver problemas de regresión lineal, mientras que el perceptrón no puede.' },
            { question: '¿Qué significa que ADALINE utilice aprendizaje continuo?', options: ['Que puede aprender de un flujo continuo de datos.', 'Que sus pesos se ajustan en pequeños incrementos a lo largo de un ciclo de entrenamiento.', 'Que puede aprender de un flujo continuo de datos y que sus pesos se ajustan de forma continua.', 'Que el entrenamiento no tiene un punto de parada.'], correctAnswer: 'Que sus pesos se ajustan en pequeños incrementos a lo largo de un ciclo de entrenamiento.' },
            { question: '¿Cómo se evalúa el rendimiento de ADALINE en tareas de clasificación?', options: ['Con la precisión, pero también con el error cuadrático medio que se busca minimizar.', 'Solo con la precisión.', 'Con el tiempo de entrenamiento.', 'Con la cantidad de datos que utiliza.'], correctAnswer: 'Con la precisión, pero también con el error cuadrático medio que se busca minimizar.' },
            { question: '¿Qué tipo de problemas puede resolver ADALINE?', options: ['Solo problemas de clasificación linealmente separables.', 'Problemas de regresión lineal y de clasificación linealmente separables.', 'Solo problemas no lineales.', 'Problemas de agrupamiento.'], correctAnswer: 'Problemas de regresión lineal y de clasificación linealmente separables.' },
            { question: '¿Qué relación tiene ADALINE con el método de mínimos cuadrados?', options: ['ADALINE es una versión del método de mínimos cuadrados con un proceso de actualización iterativo de los pesos.', 'ADALINE es un método de mínimos cuadrados pero solo para clasificación.', 'No hay ninguna relación.', 'ADALINE y el método de mínimos cuadrados son idénticos.'], correctAnswer: 'ADALINE es una versión del método de mínimos cuadrados con un proceso de actualización iterativo de los pesos.' },
            { question: '¿Qué implicaciones tiene el uso de ADALINE en sistemas adaptativos?', options: ['Permite que los sistemas se adapten a nuevos datos de entrada y que sus pesos se ajusten dinámicamente.', 'No tiene implicaciones en sistemas adaptativos.', 'Solo puede usarse en sistemas no adaptativos.', 'Hace que los sistemas sean más complejos y difíciles de usar.'], correctAnswer: 'Permite que los sistemas se adapten a nuevos datos de entrada y que sus pesos se ajusten dinámicamente.' }
        ];

        let selectedQuestions = [];
        let currentQuestionIndex = 0;
        let score = 0;
        let timer;
        let timeLeft = 30;
        let userName = null;
        let userEmail = null;

        const userInfoSection = document.getElementById('user-info');
        const quizContainer = document.getElementById('quiz-container');
        const resultsContainer = document.getElementById('results-container');
        const userForm = document.getElementById('user-form');
        const questionText = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const timerDisplay = document.getElementById('timer');
        const finalNameSpan = document.getElementById('final-name');
        const finalEmailSpan = document.getElementById('final-email');
        const scoreSpan = document.getElementById('score');
        const downloadButton = document.getElementById('download-score');

        // Función para seleccionar 10 preguntas aleatorias de la lista completa
        const selectRandomQuestions = () => {
            const shuffled = [...allQuestions].sort(() => 0.5 - Math.random());
            selectedQuestions = shuffled.slice(0, 10);
        };

        // Función para mostrar la pregunta actual y sus opciones
        const showQuestion = () => {
            if (currentQuestionIndex < selectedQuestions.length) {
                const currentQuestion = selectedQuestions[currentQuestionIndex];
                questionText.textContent = currentQuestion.question;
                optionsContainer.innerHTML = '';
                
                // Mezcla las opciones para que no siempre estén en el mismo orden
                const shuffledOptions = [...currentQuestion.options].sort(() => 0.5 - Math.random());
                
                shuffledOptions.forEach(option => {
                    const button = document.createElement('div');
                    button.className = 'option';
                    button.textContent = option;
                    button.addEventListener('click', () => handleAnswer(option, currentQuestion.correctAnswer));
                    optionsContainer.appendChild(button);
                });
                
                resetTimer();
            } else {
                endQuiz();
            }
        };

        // Maneja la respuesta del usuario
        const handleAnswer = (selectedOption, correctAnswer) => {
            clearInterval(timer);
            
            // Deshabilita todas las opciones después de la selección
            const options = optionsContainer.querySelectorAll('.option');
            options.forEach(option => {
                option.style.pointerEvents = 'none';
                if (option.textContent === correctAnswer) {
                    option.classList.add('correct');
                } else if (option.textContent === selectedOption) {
                    option.classList.add('incorrect');
                }
            });

            if (selectedOption === correctAnswer) {
                score++;
            }

            setTimeout(() => {
                currentQuestionIndex++;
                showQuestion();
            }, 1500); // 1.5 segundos para ver la respuesta correcta
        };

        // Control del temporizador
        const startTimer = () => {
            timeLeft = 30;
            timerDisplay.textContent = `Tiempo restante: ${timeLeft}s`;
            
            timer = setInterval(() => {
                timeLeft--;
                timerDisplay.textContent = `Tiempo restante: ${timeLeft}s`;
                if (timeLeft <= 0) {
                    clearInterval(timer);
                    // Si el tiempo se acaba, avanza a la siguiente pregunta
                    const options = optionsContainer.querySelectorAll('.option');
                    options.forEach(option => option.style.pointerEvents = 'none');
                    setTimeout(() => {
                        currentQuestionIndex++;
                        showQuestion();
                    }, 1500);
                }
            }, 1000);
        };

        const resetTimer = () => {
            clearInterval(timer);
            startTimer();
        };

        // Fin del examen, muestra los resultados y envía el correo
        const endQuiz = () => {
            quizContainer.classList.add('hidden');
            resultsContainer.classList.remove('hidden');
            
            finalNameSpan.textContent = userName;
            finalEmailSpan.textContent = userEmail;
            scoreSpan.textContent = score;

            // Llama a la función para enviar los resultados
            sendResultsToServer(userName, userEmail, score);

            // Guarda el correo en localStorage para evitar reintentos
            localStorage.setItem(userEmail, 'completed');
        };

        // Manejo del formulario de inicio
        userForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const fullName = document.getElementById('full-name').value.trim();
            const email = document.getElementById('email').value.trim();
            
            // Valida si el usuario ya ha contestado el examen
            if (localStorage.getItem(email)) {
                // He usado un alert, pero se recomienda usar un modal o mensaje en el UI para una mejor experiencia de usuario.
                alert('Ya has contestado este examen. No se permiten reintentos.');
                return;
            }

            userName = fullName;
            userEmail = email;
            
            userInfoSection.classList.add('hidden');
            quizContainer.classList.remove('hidden');
            
            selectRandomQuestions();
            showQuestion();
        });

        // Función para enviar los resultados al servidor (simulada)
        // **IMPORTANTE**: Necesitas un servidor backend para que esto funcione
        const sendResultsToServer = async (name, email, finalScore) => {
            const serverUrl = 'https://tu-servidor-backend.com/api/send-email'; // **REEMPLAZA ESTA URL CON LA DE TU SERVIDOR**
            const professorEmail = 'karla.ayard@universidad-une.com';

            try {
                const response = await fetch(serverUrl, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        name,
                        email,
                        score: finalScore,
                        professorEmail
                    }),
                });

                if (response.ok) {
                    console.log('Resultados enviados por correo correctamente.');
                } else {
                    console.error('Error al enviar los resultados por correo.');
                }
            } catch (error) {
                console.error('Error de red:', error);
            }
        };

        // Funcionalidad para descargar la puntuación
        downloadButton.addEventListener('click', () => {
            const textToDownload = `
Resultados del Examen
--------------------
Nombre: ${userName}
Correo: ${userEmail}
Puntuación: ${score} / 10
            `;
            
            const blob = new Blob([textToDownload], { type: 'text/plain' });
            const url = URL.createObjectURL(blob);
            
            const a = document.createElement('a');
            a.href = url;
            a.download = `examen-puntuacion-${userName.replace(/\s/g, '-')}.txt`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
        });
    </script>
</body>
</html>
