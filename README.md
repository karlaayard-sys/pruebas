<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Examen de Inteligencia Artificial</title>
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
            <h1>Examen de Inteligencia Artificial</h1>
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
            { question: '¿Qué es la Inteligencia Artificial (IA)?', options: ['La capacidad de un sistema para realizar tareas que requieren inteligencia humana.', 'Un campo de la computación que se enfoca en el desarrollo de software de código abierto.', 'El estudio de cómo los humanos interactúan con las computadoras.', 'La rama de la robótica que se centra en el diseño de robots humanoides.'], correctAnswer: 'La capacidad de un sistema para realizar tareas que requieren inteligencia humana.' },
            { question: '¿Cuál es el objetivo principal de la IA?', options: ['Crear computadoras que piensen y actúen como humanos.', 'Automatizar procesos industriales para eliminar la mano de obra.', 'Mejorar la velocidad de los procesadores.', 'Desarrollar sistemas de entretenimiento más realistas.'], correctAnswer: 'Crear computadoras que piensen y actúen como humanos.' },
            { question: '¿Qué diferencia hay entre IA fuerte y débil?', options: ['La IA fuerte es más avanzada que la débil.', 'La IA débil se enfoca en tareas específicas, mientras que la fuerte tiene capacidades cognitivas generales.', 'La IA fuerte es un mito, la débil es la que existe actualmente.', 'La IA débil se basa en software y la fuerte en hardware.'], correctAnswer: 'La IA débil se enfoca en tareas específicas, mientras que la fuerte tiene capacidades cognitivas generales.' },
            { question: '¿Qué es el Aprendizaje Automático (Machine Learning)?', options: ['Un tipo de inteligencia artificial que permite a las máquinas aprender de los datos sin ser programadas explícitamente.', 'Un software que permite la creación de videojuegos de alta calidad.', 'Un proceso de la robótica para ensamblar piezas de forma eficiente.', 'Una técnica de programación que utiliza el lenguaje C++.'], correctAnswer: 'Un tipo de inteligencia artificial que permite a las máquinas aprender de los datos sin ser programadas explícitamente.' },
            { question: '¿Qué es el Deep Learning (Aprendizaje Profundo)?', options: ['Un subcampo del Machine Learning que se enfoca en el desarrollo de modelos que imitan la forma en que el cerebro humano procesa la información.', 'Una técnica de ciberseguridad para proteger datos sensibles.', 'Un método de programación que utiliza algoritmos muy complejos.', 'La programación de robots que pueden moverse en aguas profundas.'], correctAnswer: 'Un subcampo del Machine Learning que se enfoca en el desarrollo de modelos que imitan la forma en que el cerebro humano procesa la información.' },
            { question: '¿Qué es una red neuronal artificial?', options: ['Un sistema informático que se basa en la estructura del cerebro humano para procesar información.', 'Una red de computadoras interconectadas para compartir datos.', 'Una técnica de inteligencia artificial que se utiliza para la creación de bases de datos.', 'Un tipo de software para el diseño gráfico.'], correctAnswer: 'Un sistema informático que se basa en la estructura del cerebro humano para procesar información.' },
            { question: '¿Qué es el Procesamiento del Lenguaje Natural (NLP)?', options: ['Un método para la creación de programas que pueden traducir idiomas de forma instantánea.', 'La capacidad de una máquina para entender, interpretar y generar lenguaje humano.', 'Una técnica de programación para el diseño de interfaces de usuario.', 'El estudio de la forma en que el lenguaje se desarrolla en los niños.'], correctAnswer: 'La capacidad de una máquina para entender, interpretar y generar lenguaje humano.' },
            { question: '¿Cuál de los siguientes no es un tipo de aprendizaje automático?', options: ['Aprendizaje supervisado.', 'Aprendizaje no supervisado.', 'Aprendizaje por refuerzo.', 'Aprendizaje sensorial.'], correctAnswer: 'Aprendizaje sensorial.' },
            { question: '¿Qué es el reconocimiento de imágenes?', options: ['Una técnica de IA que permite a las computadoras identificar y clasificar objetos en imágenes y videos.', 'Un software para la edición de fotografías.', 'El proceso de crear imágenes a partir de datos.', 'Una herramienta para la creación de animaciones.'], correctAnswer: 'Una técnica de IA que permite a las computadoras identificar y clasificar objetos en imágenes y videos.' },
            { question: '¿Qué es la robótica?', options: ['El campo de la ingeniería que se enfoca en la creación de máquinas autónomas.', 'El estudio de la historia de los robots.', 'Un tipo de inteligencia artificial que solo se usa en fábricas.', 'La programación de robots para jugar ajedrez.'], correctAnswer: 'El campo de la ingeniería que se enfoca en la creación de máquinas autónomas.' }
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

