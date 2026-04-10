# Examen-adulto
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Examen Integral</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; }
        .correct { border-color: #10b981 !important; background-color: #f0fdf4 !important; }
        .incorrect { border-color: #ef4444 !important; background-color: #fef2f2 !important; }
        .option-btn:disabled { cursor: default; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 min-h-screen">

    <nav class="bg-white border-b border-slate-200 sticky top-0 z-50 py-4 px-6 flex justify-between items-center shadow-sm">
        <h1 class="font-bold text-xl text-indigo-700">Simulador <span class="text-slate-400 font-light">42 Reactivos</span></h1>
        <div class="flex items-center space-x-4">
            <div id="timer" class="font-mono font-bold text-lg bg-slate-100 px-3 py-1 rounded-lg">00:00</div>
        </div>
    </nav>

    <main class="max-w-4xl mx-auto p-6">
        <!-- Inicio -->
        <div id="start-screen" class="text-center py-12 bg-white rounded-3xl shadow-sm border border-slate-200 p-8">
            <h2 class="text-3xl font-bold mb-4">Examen Integral de Medicina Interna</h2>
            <p class="text-slate-600 mb-8">Se han seleccionado 42 reactivos aleatorios de los bancos de Cardio, Resp, Urinario y Locomotor. Formato de tres opciones (A, B, C) con retroalimentación inmediata.</p>
            <button onclick="startQuiz()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-10 py-4 rounded-2xl font-bold shadow-lg transition-transform hover:scale-105">
                Iniciar Examen
            </button>
        </div>

        <!-- Quiz -->
        <div id="quiz-container" class="hidden">
            <div class="mb-6">
                <div class="flex justify-between text-sm font-bold mb-2">
                    <span id="q-category" class="text-indigo-600 uppercase">CATEGORÍA</span>
                    <span id="q-counter" class="text-slate-400">Pregunta 1/42</span>
                </div>
                <div class="w-full bg-slate-200 rounded-full h-2.5">
                    <div id="progress-bar" class="bg-indigo-600 h-2.5 rounded-full transition-all" style="width: 0%"></div>
                </div>
            </div>

            <div class="bg-white rounded-3xl shadow-sm border border-slate-200 p-8 mb-6">
                <h3 id="question-text" class="text-xl font-bold text-slate-800 leading-relaxed mb-8">Pregunta</h3>
                <div id="options-grid" class="space-y-4">
                    <!-- Opciones A, B, C -->
                </div>
            </div>

            <div class="flex justify-end">
                <button id="btn-next" onclick="nextQuestion()" class="hidden bg-slate-900 text-white px-8 py-3 rounded-xl font-bold hover:bg-slate-800">
                    Siguiente <i class="fas fa-arrow-right ml-2"></i>
                </button>
            </div>
        </div>

        <!-- Resultados -->
        <div id="results-screen" class="hidden">
            <div class="bg-white rounded-3xl shadow-xl p-10 text-center mb-8 border border-slate-200">
                <h2 class="text-4xl font-black mb-4">Resultado Final</h2>
                <div class="flex justify-center space-x-12 my-8">
                    <div>
                        <p class="text-5xl font-black text-indigo-600" id="res-score">0</p>
                        <p class="text-slate-400 font-medium uppercase tracking-widest text-xs mt-2">Aciertos</p>
                    </div>
                    <div>
                        <p class="text-5xl font-black text-slate-800" id="res-percent">0%</p>
                        <p class="text-slate-400 font-medium uppercase tracking-widest text-xs mt-2">Calificación</p>
                    </div>
                </div>
                <div id="res-feedback" class="p-6 rounded-2xl bg-indigo-50 text-indigo-800 font-semibold mb-6 italic"></div>
                <button onclick="location.reload()" class="bg-indigo-600 text-white px-8 py-3 rounded-xl font-bold shadow-md">Nuevo Intento Aleatorio</button>
            </div>

            <h3 class="text-2xl font-bold mb-6">Revisión Académica</h3>
            <div id="detailed-review" class="space-y-6"></div>
        </div>
    </main>

    <script>
        // Banco consolidado basado en los documentos proporcionados (42 reactivos clave)
        const rawBank = [
            // CARDIO (NOM-030, GPC, LIFE)
            { cat: "Cardiovascular", q: "Mujer de 48 años con TA 148/94 mmHg en tres tomas. Sin daño a órgano blanco ni comorbilidades. ¿Conducta inicial?", c: "Estadio 2; iniciar cambios estilo de vida + fármaco", o: ["Estadio 1; estilo de vida por 6 meses", "Bata blanca; solo monitoreo"], j: "Según NOM-030, cifras ≥140/90 en estadio 2 ameritan fármaco inicial si hay riesgo cardiovascular." },
            { cat: "Cardiovascular", q: "Fármaco de elección en hipertenso con DM2 y microalbuminuria:", c: "IECA o ARA-II", o: ["Betabloqueadores", "Tiazidas"], j: "Son fármacos renoprotectores que reducen la presión intraglomerular." },
            { cat: "Cardiovascular", q: "Estudio LIFE: ¿Qué fármaco redujo más el riesgo de ACV y regresión de HVI?", c: "Losartán", o: ["Atenolol", "Amlodipino"], j: "Los ARA-II como Losartán demostraron superioridad en regresión de masa ventricular vs betabloqueadores." },
            { cat: "Cardiovascular", q: "Objetivo de TA en paciente con Hipertrofia Ventricular Izquierda:", c: "< 130/80 mmHg", o: ["< 140/90 mmHg", "< 150/90 mmHg"], j: "Las guías actuales (ESC/AHA) bajan la meta en daño a órgano blanco." },
            { cat: "Cardiovascular", q: "Criterio electrocardiográfico de Sokolow-Lyon para HVI:", c: "S en V1 + R en V5 o V6 > 35 mm", o: ["R en aVL > 11 mm", "Eje a la izquierda < -30°"], j: "Es el criterio de voltaje más utilizado en la práctica clínica." },
            { cat: "Cardiovascular", q: "Efecto adverso no grave más común de los IECA:", c: "Tos seca persistente", o: ["Edema maleolar", "Hipopotasemia"], j: "Causado por la acumulación de bradicinina en el pulmón." },
            
            // RESPIRATORIO (IRA, SDRA, TB, CANCER)
            { cat: "Respiratorio", q: "Definición de Insuficiencia Respiratoria tipo II:", c: "PaO2 < 60 y PaCO2 > 45 mmHg", o: ["PaO2 < 60 y PaCO2 < 35", "PaO2 < 80 y PaCO2 > 40"], j: "La tipo II es hipercápnica o ventilatoria." },
            { cat: "Respiratorio", q: "SDRA Moderado según Criterios de Berlín (PaO2/FiO2):", c: "100 a 200 mmHg", o: ["200 a 300 mmHg", "< 100 mmHg"], j: "Leve (200-300), Moderado (100-200), Grave (<100)." },
            { cat: "Respiratorio", q: "Signo clínico de fracaso de VNI:", c: "Deterioro del estado neurológico", o: ["Aumento de expectoración", "Fiebre de 38°C"], j: "El deterioro sensorial indica hipoxia/hipercapnia severa y requiere intubación." },
            { cat: "Respiratorio", q: "Esquema TAES fase intensiva en México:", c: "2 meses con HRZE", o: ["2 meses con HR", "4 meses con HRZE"], j: "Isoniazida, Rifampicina, Pirazinamida y Etambutol por 60 dosis." },
            { cat: "Respiratorio", q: "Tríada del Síndrome de Horner en Tumor de Pancoast:", c: "Ptosis, miosis y anhidrosis", o: ["Tos, hemoptisis y disnea", "Edema, cianosis y dolor"], j: "Por afectación del ganglio estrellado simpático." },
            { cat: "Respiratorio", q: "Tratamiento de elección en Tumor de Pancoast resecable:", c: "Quimiorradioterapia de inducción + Cirugía", o: ["Cirugía de inicio", "Solo quimioterapia paliativa"], j: "Protocolo del estudio INT 0160 para mejorar resecabilidad." },
            { cat: "Respiratorio", q: "Efecto metabólico de la Pirazinamida:", c: "Hiperuricemia", o: ["Hipoglucemia", "Hipercalcemia"], j: "La pirazinamida inhibe la excreción renal de ácido úrico." },
            { cat: "Respiratorio", q: "Tipo histológico de cáncer de pulmón más frecuente:", c: "Adenocarcinoma", o: ["Carcinoma epidermoide", "Células pequeñas"], j: "Es el más común actualmente en fumadores y no fumadores." },

            // URINARIO (IVU, PROSTATITIS, CANCER)
            { cat: "Urinario", q: "Tratamiento de primera línea en Cistitis no complicada:", c: "Nitrofurantoína x 5 días", o: ["Ciprofloxacino x 3 días", "Amoxicilina x 7 días"], j: "Guía GPC/EAU: Nitrofurantoína tiene menor resistencia bacteriana." },
            { cat: "Urinario", q: "Conducta ante Bacteriuria Asintomática en embarazada:", c: "Tratamiento antibiótico obligatorio", o: ["Observación", "Repetir urocultivo en un mes"], j: "Previene prematurez y pielonefritis." },
            { cat: "Urinario", q: "Hallazgo al tacto rectal en Prostatitis Aguda:", c: "Próstata caliente y muy dolorosa", o: ["Próstata pétrea e irregular", "Próstata blanda no dolorosa"], j: "Es un diagnóstico clínico; el masaje prostático está contraindicado." },
            { cat: "Urinario", q: "Duración del tratamiento en Prostatitis Bacteriana Aguda:", c: "4 semanas", o: ["7 días", "14 días"], j: "Se requiere tiempo prolongado para asegurar penetración tisular." },
            { cat: "Urinario", q: "Marcador de tamizaje en Cáncer de Próstata:", c: "Antígeno Prostático Específico (PSA)", o: ["Alfa-fetoproteína", "CA-125"], j: "Inicia a los 50 años en población general." },
            { cat: "Urinario", q: "Manejo en Ca. Próstata metastásico de alto volumen:", c: "ADT + Docetaxel", o: ["Orquiectomía sola", "Radioterapia a próstata"], j: "La combinación aumenta supervivencia global según estudio CHAARTED." },
            { cat: "Urinario", q: "Definición de Cáncer de Próstata Resistente a Castración:", c: "Progresión con Testosterona < 50 ng/dL", o: ["PSA alto con Testosterona > 100", "PSA bajo con metástasis"], j: "Niveles de castración química se definen por < 50 ng/dL." },

            // LOCOMOTOR (OA, AR)
            { cat: "Locomotor", q: "Característica del dolor en Osteoartrosis (OA):", c: "Carácter mecánico (mejora en reposo)", o: ["Carácter inflamatorio (peor en reposo)", "Dolor nocturno persistente"], j: "El dolor de la OA cede al quitar la carga de la articulación." },
            { cat: "Locomotor", q: "Localización de Nódulos de Heberden:", c: "Interfalángicas distales", o: ["Interfalángicas proximales", "Metacarpofalángicas"], j: "Diferencia clave con la AR que afecta proximales (Bouchard)." },
            { cat: "Locomotor", q: "Hallazgo radiológico más específico de OA:", c: "Osteofitos", o: ["Erosiones óseas", "Geodas gigantes"], j: "Es la respuesta hipertrófica del hueso subcondral." },
            { cat: "Locomotor", q: "Duración de rigidez matutina en Artritis Reumatoide:", c: "> 1 hora", o: ["< 30 minutos", "No presenta rigidez"], j: "Es un criterio clínico fundamental de actividad inflamatoria." },
            { cat: "Locomotor", q: "Marcador más específico de Artritis Reumatoide:", c: "Anti-CCP (Anticitrulina)", o: ["Factor Reumatoide", "Proteína C Reactiva"], j: "Especificidad > 95% vs Factor Reumatoide." },
            { cat: "Locomotor", q: "FARME de primera elección en Artritis Reumatoide:", c: "Metotrexato", o: ["Leflunomida", "Sulfasalazina"], j: "Ancla del tratamiento según ACR/EULAR." },
            { cat: "Locomotor", q: "Dosis máxima de Metotrexato en AR:", c: "25 mg/semana", o: ["15 mg/semana", "50 mg/semana"], j: "Dosis mayores no aumentan eficacia pero sí toxicidad hematológica." },
            { cat: "Locomotor", q: "Signo radiológico de daño estructural avanzado en AR:", c: "Erosiones óseas", o: ["Esclerosis subcondral", "Osteofitos marginales"], j: "Indican progresión destructiva mediada por pannus." },
            { cat: "Locomotor", q: "Estrategia Treat-to-Target (T2T) en AR:", c: "Lograr remisión o baja actividad", o: ["Solo control del dolor", "Evitar el uso de esteroides"], j: "Ajuste mensual de fármacos hasta meta clínica." },
            
            // ADICIONALES PARA LLEGAR A 42 (Mezcla de temas clave)
            { cat: "Cardiovascular", q: "Diurético de elección en HAS no complicada (NOM-030):", c: "Tiazidas (Clortalidona/Hidroclorotiazida)", o: ["Furosemida", "Espironolactona"], j: "Tienen mayor evidencia en reducción de eventos cardiovasculares." },
            { cat: "Urinario", q: "Factor riesgo más común para IVU recurrente en jóvenes:", c: "Frecuencia de actividad sexual", o: ["Uso de baños públicos", "Higiene deficiente"], j: "Factor conductual más asociado en guías internacionales." },
            { cat: "Respiratorio", q: "Causa más frecuente de SDRA:", c: "Sepsis", o: ["Contusión pulmonar", "Pancreatitis"], j: "La sepsis (pulmonar o extrapulmonar) causa el 40% de los casos." },
            { cat: "Locomotor", q: "Tratamiento de primera línea en OA de rodilla (GPC):", c: "Paracetamol o AINE tópico", o: ["Glucosamina", "Cirugía"], j: "Se prefiere manejo conservador tópico inicialmente." },
            { cat: "Cardiovascular", q: "Meta de TA en población general < 60 años:", c: "< 140/90 mmHg", o: ["< 130/80 mmHg", "< 120/80 mmHg"], j: "Meta estándar por NOM-030 y JNC-8." },
            { cat: "Urinario", q: "Prevención de IVU postcoital:", c: "Dosis única de antibiótico postcoito", o: ["Uso de antisépticos tópicos", "Micción diferida"], j: "Estrategia efectiva en mujeres con recurrencia clara." },
            { cat: "Respiratorio", q: "Hallazgo en Rx de Tórax en Tuberculosis Primaria:", c: "Complejo de Ghon / Infiltrados apicales", o: ["Derrame pleural masivo", "Atelectasia basal"], j: "Típica reactivación en lóbulos superiores (ápices)." },
            { cat: "Respiratorio", q: "Signo de fatiga muscular en Insuficiencia Respiratoria:", c: "Respiración paradójica", o: ["Taquipnea leve", "Tos productiva"], j: "Indica agotamiento diafragmático inminente." },
            { cat: "Cardiovascular", q: "Urgencia hipertensiva se define como:", c: "TA > 180/120 sin daño agudo a órgano blanco", o: ["TA > 160/100 con cefalea", "TA > 210/130 con edema agudo"], j: "Diferencia clave con la emergencia que sí tiene daño agudo." },
            { cat: "Locomotor", q: "Articulación de carga más afectada en OA primaria:", c: "Rodilla", o: ["Cadera", "Tobillo"], j: "La gonartrosis es la principal causa de discapacidad en el anciano." },
            { cat: "Urinario", q: "Edad de inicio de tamizaje Ca. Próstata con AHF:", c: "40-45 años", o: ["50 años", "35 años"], j: "Se adelanta si hay familiares de primer grado afectados." },
            { cat: "Cardiovascular", q: "Mecanismo de acción de los ARA-II:", c: "Antagonismo del receptor AT1 de Angiotensina II", o: ["Inhibición de la ECA", "Bloqueo de canales de calcio"], j: "Evitan el efecto vasoconstrictor y de remodelado de la Angiotensina II." }
        ];

        let quizData = [];
        let currentIdx = 0;
        let score = 0;
        let results = [];
        let timerSeconds = 0;
        let interval;

        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        function startQuiz() {
            quizData = shuffle([...rawBank]).slice(0, 42);
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('quiz-container').classList.remove('hidden');
            startTimer();
            showQuestion();
        }

        function startTimer() {
            interval = setInterval(() => {
                timerSeconds++;
                const m = String(Math.floor(timerSeconds / 60)).padStart(2, '0');
                const s = String(timerSeconds % 60).padStart(2, '0');
                document.getElementById('timer').innerText = `${m}:${s}`;
            }, 1000);
        }

        function showQuestion() {
            const data = quizData[currentIdx];
            document.getElementById('q-category').innerText = data.cat;
            document.getElementById('q-counter').innerText = `Pregunta ${currentIdx + 1}/42`;
            document.getElementById('question-text').innerText = data.q;
            document.getElementById('progress-bar').style.width = `${(currentIdx / 42) * 100}%`;

            const optionsGrid = document.getElementById('options-grid');
            optionsGrid.innerHTML = '';

            const options = shuffle([data.c, ...data.o]);
            const labels = ['A', 'B', 'C'];

            options.forEach((opt, i) => {
                const btn = document.createElement('button');
                btn.className = "option-btn w-full text-left p-5 rounded-2xl border-2 border-slate-100 bg-white hover:border-indigo-200 transition-all flex items-center group";
                btn.innerHTML = `
                    <span class="w-10 h-10 rounded-xl bg-slate-50 flex items-center justify-center mr-4 text-slate-400 font-bold group-hover:bg-indigo-50 group-hover:text-indigo-600 transition-colors">${labels[i]}</span>
                    <span class="text-slate-700 font-medium">${opt}</span>
                `;
                btn.onclick = () => selectOption(opt, data.c, btn);
                optionsGrid.appendChild(btn);
            });
            document.getElementById('btn-next').classList.add('hidden');
        }

        function selectOption(selected, correct, btn) {
            const btns = document.querySelectorAll('.option-btn');
            btns.forEach(b => b.onclick = null);

            const isCorrect = selected === correct;
            if (isCorrect) {
                btn.classList.add('correct');
                score++;
            } else {
                btn.classList.add('incorrect');
                btns.forEach(b => {
                    if (b.innerText.includes(correct)) b.classList.add('correct');
                });
            }

            results.push({ q: quizData[currentIdx], isCorrect, selected });
            document.getElementById('btn-next').classList.remove('hidden');
        }

        function nextQuestion() {
            currentIdx++;
            if (currentIdx < 42) {
                showQuestion();
            } else {
                showResults();
            }
        }

        function showResults() {
            clearInterval(interval);
            document.getElementById('quiz-container').classList.add('hidden');
            document.getElementById('results-screen').classList.remove('hidden');
            document.getElementById('progress-bar').style.width = "100%";

            const p = Math.round((score / 42) * 100);
            document.getElementById('res-score').innerText = score;
            document.getElementById('res-percent').innerText = `${p}%`;

            const feedback = document.getElementById('res-feedback');
            if (p >= 90) feedback.innerText = "¡Excelente! Nivel de Residente Destacado.";
            else if (p >= 70) feedback.innerText = "Buen desempeño. Estás listo para el ENARM, pero revisa los fallos.";
            else feedback.innerText = "Sigue estudiando. Revisa las justificaciones clínicas abajo.";

            const review = document.getElementById('detailed-review');
            review.innerHTML = '';
            results.forEach((r, idx) => {
                const card = document.createElement('div');
                card.className = `p-6 rounded-2xl border-l-8 bg-white shadow-sm ${r.isCorrect ? 'border-emerald-500' : 'border-rose-500'}`;
                card.innerHTML = `
                    <p class="text-[10px] font-black uppercase text-slate-400 mb-1">${r.q.cat}</p>
                    <h4 class="font-bold text-slate-800 mb-3">${idx+1}. ${r.q.q}</h4>
                    <p class="text-sm mb-2"><span class="font-bold">Respuesta:</span> ${r.selected} ${r.isCorrect ? '✅' : '❌'}</p>
                    <div class="mt-4 p-4 bg-slate-50 rounded-xl text-xs text-slate-600 border-t border-slate-100 italic">
                        <strong class="text-indigo-600 not-italic block mb-1 uppercase tracking-tighter">Justificación:</strong> ${r.q.j}
                    </div>
                `;
                review.appendChild(card);
            });
        }
    </script>
</body>
</html>
