<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Examen Integral Medicina Interna</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .correct { border-color: #10b981 !important; background-color: #f0fdf4 !important; }
        .incorrect { border-color: #ef4444 !important; background-color: #fef2f2 !important; }
        .option-btn:disabled { cursor: default; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 min-h-screen">

    <nav class="bg-white border-b border-slate-200 sticky top-0 z-50 py-4 px-6 flex justify-between items-center shadow-sm">
        <h1 class="font-bold text-xl text-indigo-700">Simulador <span class="text-slate-400 font-light">PRO</span></h1>
        <div class="flex items-center space-x-4">
            <div id="timer" class="font-mono font-bold text-lg bg-slate-100 px-3 py-1 rounded-lg">00:00</div>
        </div>
    </nav>

    <main class="max-w-4xl mx-auto p-6">
        <!-- Pantalla Inicial -->
        <div id="start-screen" class="text-center py-12 bg-white rounded-3xl shadow-sm border border-slate-200 p-8">
            <div class="w-20 h-20 bg-indigo-100 text-indigo-600 rounded-full flex items-center justify-center mx-auto mb-6">
                <i class="fas fa-user-md text-3xl"></i>
            </div>
            <h2 class="text-3xl font-bold mb-4">Evaluación Integral del Adulto</h2>
            <p class="text-slate-600 mb-8 max-w-xl mx-auto">
                Este examen selecciona **42 preguntas aleatorias** de los temas de Cardiovascular, Respiratorio, Urinario y Locomotor. 
                Las opciones y el orden cambian en cada intento para evitar la memorización por posición.
            </p>
            <button onclick="startQuiz()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-10 py-4 rounded-2xl font-bold shadow-lg transition-transform hover:scale-105">
                Iniciar Examen Aleatorio
            </button>
        </div>

        <!-- Pantalla de Preguntas -->
        <div id="quiz-container" class="hidden">
            <div class="mb-6">
                <div class="flex justify-between text-sm font-bold mb-2">
                    <span id="q-category" class="text-indigo-600 uppercase tracking-widest">CATEGORÍA</span>
                    <span id="q-counter" class="text-slate-400">Pregunta 1/42</span>
                </div>
                <div class="w-full bg-slate-200 rounded-full h-2.5">
                    <div id="progress-bar" class="bg-indigo-600 h-2.5 rounded-full transition-all" style="width: 0%"></div>
                </div>
            </div>

            <div class="bg-white rounded-3xl shadow-sm border border-slate-200 p-8 mb-6">
                <h3 id="question-text" class="text-xl font-bold text-slate-800 leading-relaxed mb-8">Pregunta</h3>
                <div id="options-grid" class="space-y-4"></div>
            </div>

            <div class="flex justify-end">
                <button id="btn-next" onclick="nextQuestion()" class="hidden bg-slate-900 text-white px-8 py-3 rounded-xl font-bold hover:bg-gray-800 transition-all shadow-lg">
                    Siguiente <i class="fas fa-arrow-right ml-2"></i>
                </button>
            </div>
        </div>

        <!-- Pantalla de Resultados -->
        <div id="results-screen" class="hidden">
            <div class="bg-white rounded-3xl shadow-xl p-10 text-center mb-8 border border-slate-200">
                <h2 class="text-4xl font-black mb-4">Resultados</h2>
                <div class="flex justify-center space-x-12 my-8">
                    <div>
                        <p class="text-5xl font-black text-indigo-600" id="res-score">0</p>
                        <p class="text-slate-400 font-medium uppercase tracking-widest text-xs mt-2">Aciertos</p>
                    </div>
                    <div>
                        <p class="text-5xl font-black text-slate-800" id="res-percent">0%</p>
                        <p class="text-slate-400 font-medium uppercase tracking-widest text-xs mt-2">Puntaje</p>
                    </div>
                </div>
                <div id="res-feedback" class="p-6 rounded-2xl bg-indigo-50 text-indigo-800 font-semibold mb-6 italic border border-indigo-100"></div>
                <button onclick="location.reload()" class="bg-indigo-600 text-white px-8 py-3 rounded-xl font-bold shadow-md hover:bg-indigo-700 transition-all">Reintentar Nuevo Examen</button>
            </div>

            <h3 class="text-2xl font-bold mb-6 text-slate-800 px-4"><i class="fas fa-clipboard-list mr-3 text-indigo-600"></i>Revisión Académica</h3>
            <div id="detailed-review" class="space-y-6"></div>
        </div>
    </main>

    <script>
        // BANCO TOTAL DE PREGUNTAS (Extrapolado de documentos)
        const MASTER_BANK = [
            // CARDIOVASCULAR
            { cat: "Cardiovascular", q: "Mujer de 48 años con TA promedio de 148/94 mmHg en consultas separadas. Fondo de ojo normal, IMC 27. ¿Clasificación y conducta?", c: "Hipertensión estadio 2; iniciar cambios estilo de vida + fármaco", o: ["Hipertensión estadio 1; solo estilo de vida por 6 meses", "Bata blanca; observación"], j: "NOM-030 y GPC: Estadio 2 (≥140/90) requiere tratamiento farmacológico inicial en pacientes con riesgo." },
            { cat: "Cardiovascular", q: "Paciente hipertenso con DM2 y microalbuminuria. ¿Fármaco de elección?", c: "IECA o ARA-II", o: ["Betabloqueadores", "Calcioantagonistas dihidropiridínicos"], j: "Ofrecen nefroprotección al disminuir la presión intraglomerular y reducir la proteinuria." },
            { cat: "Cardiovascular", q: "Estudio LIFE: ¿Qué ARA-II demostró mayor regresión de HVI y reducción de ACV vs Atenolol?", c: "Losartán", o: ["Telmisartán", "Valsartán"], j: "El estudio LIFE evidenció que Losartán redujo significativamente la masa ventricular y eventos vasculares." },
            { cat: "Cardiovascular", q: "Hombre de 62 años con TA 210/130 mmHg, cefalea severa, sin daño agudo a órgano blanco. ¿Diagnóstico?", c: "Urgencia Hipertensiva", o: ["Emergencia Hipertensiva", "Hipertensión Grado 1"], j: "La urgencia hipertensiva no presenta daño agudo a órgano blanco, a diferencia de la emergencia." },
            { cat: "Cardiovascular", q: "¿Cuál es la meta de TA en un paciente con Cardiopatía Hipertensiva e HVI?", c: "< 130/80 mmHg", o: ["< 140/90 mmHg", "< 120/80 mmHg"], j: "Guías ACC/AHA 2017 y ESC recomiendan metas más estrictas en daño a órgano blanco." },
            { cat: "Cardiovascular", q: "¿Qué diurético es de primera elección en HAS no complicada según NOM-030?", c: "Tiazidas (Clortalidona / Hidroclorotiazida)", o: ["Furosemida", "Espironolactona"], j: "Tienen mayor evidencia en reducción de morbimortalidad a largo plazo." },
            
            // RESPIRATORIO
            { cat: "Respiratorio", q: "Hombre con PaO2 48 mmHg y PaCO2 52 mmHg. ¿Qué tipo de Insuficiencia Respiratoria presenta?", c: "Tipo II (Hipercápnica)", o: ["Tipo I (Hipoxémica)", "Tipo III (Perioperatoria)"], j: "La IR tipo II se define por PaCO2 > 45 mmHg debido a fallo ventilatorio." },
            { cat: "Respiratorio", q: "Según Criterios de Berlín, ¿qué define un SDRA Grave?", c: "PaO2/FiO2 < 100 mmHg", o: ["PaO2/FiO2 100-200 mmHg", "PaO2/FiO2 200-300 mmHg"], j: "Grave es <100, Moderado 100-200 y Leve 200-300 con PEEP ≥5." },
            { cat: "Respiratorio", q: "Manejo inicial en exacerbación de EPOC con acidosis respiratoria (pH 7.28) e hipercapnia:", c: "Ventilación No Invasiva (VNI)", o: ["Intubación inmediata", "Solo oxígeno por puntas nasales"], j: "La VNI reduce la necesidad de intubación y mortalidad en exacerbaciones hipercápnicas." },
            { cat: "Respiratorio", q: "Esquema de tratamiento para TB pulmonar fase de sostén (México):", c: "4 meses (45 dosis) con Isoniazida y Rifampicina", o: ["4 meses con Rifampicina y Etambutol", "6 meses con Isoniazida sola"], j: "La fase de sostén normal en TB sensible consiste en HR por 4 meses." },
            { cat: "Respiratorio", q: "Tumor de Pancoast con Síndrome de Horner. ¿Qué estructuras se afectan para causar ptosis y miosis?", c: "Ganglio Estrellado / Cadena Simpática", o: ["Nervio Frénico", "Nervio Laríngeo Recurrente"], j: "La invasión de la cadena simpática cervical produce el síndrome de Horner ipsilateral." },
            { cat: "Respiratorio", q: "Efecto adverso visual característico del Etambutol:", c: "Neuritis óptica (alteración de percepción de colores)", o: ["Catarata subcapsular", "Retinopatía pigmentaria"], j: "Requiere monitoreo de agudeza visual y visión de colores durante el tratamiento." },
            { cat: "Respiratorio", q: "¿Cuál es el manejo multimodal estándar para Tumor de Pancoast resecable?", c: "Quimiorradioterapia de inducción + Cirugía", o: ["Solo Cirugía", "Radioterapia sola"], j: "El estudio INT 0160 demostró mejores tasas de supervivencia con inducción previa." },
            { cat: "Respiratorio", q: "Tipo de cáncer de pulmón con mayor asociación a síndromes paraneoplásicos endocrinos:", c: "Carcinoma de células pequeñas (Microcítico)", o: ["Adenocarcinoma", "Carcinoma de células grandes"], j: "Frecuentemente produce SIADH o producción de ACTH ectópica." },

            // URINARIO
            { cat: "Urinario", q: "Mujer de 24 años con disuria y polaquiuria, sin fiebre ni dolor lumbar. Diagnóstico y manejo:", c: "Cistitis aguda; Nitrofurantoína 100 mg c/12h x 5 días", o: ["Pielonefritis; Ceftriaxona IV", "Cistitis; Ciprofloxacino dosis única"], j: "GPC: Nitrofurantoína es primera línea por su perfil de resistencia en E. coli." },
            { cat: "Urinario", q: "¿Cuál es el criterio para tratar una Bacteriuria Asintomática?", c: "Mujer embarazada o previo a cirugía urológica", o: ["Anciano institucionalizado", "Paciente diabético controlado"], j: "No se trata en la mayoría, excepto en embarazo por riesgo de pielonefritis y parto prematuro." },
            { cat: "Urinario", q: "Hombre de 68 años con chorro débil, nicturia y PSA 3.2. Tacto rectal: próstata elástica, 40g. Diagnóstico:", c: "Hiperplasia Prostática Benigna (HPB)", o: ["Cáncer de próstata", "Prostatitis crónica"], j: "La consistencia elástica y el PSA en rango normal orientan a patología benigna." },
            { cat: "Urinario", q: "Manejo de Prostatitis Bacteriana Aguda con retención urinaria:", c: "Antibiótico sistémico + Cistostomía suprapúbica", o: ["Sondaje transuretral inmediato", "Masaje prostático"], j: "El sondaje uretral está contraindicado por riesgo de bacteriemia en fase aguda." },
            { cat: "Urinario", q: "Definición de Cáncer de Próstata Resistente a Castración (CPRC):", c: "Progresión con testosterona sérica < 50 ng/dL", o: ["PSA elevado con testosterona > 100", "Falla a quimioterapia previa"], j: "Se define por progresión a pesar de niveles de castración de testosterona." },
            { cat: "Urinario", q: "Esquema de elección en Ca de Próstata metastásico de alto volumen:", c: "Terapia de privación androgénica (ADT) + Docetaxel", o: ["Orquiectomía bilateral sola", "ADT + Radioterapia local"], j: "Estudio CHAARTED demostró beneficio en supervivencia al añadir quimio a la ADT." },
            { cat: "Urinario", q: "Edad recomendada para iniciar tamizaje de Ca Próstata si no hay factores de riesgo:", c: "50 años", o: ["40 años", "65 años"], j: "Inicia a los 50 con PSA y tacto rectal, o a los 40-45 si hay antecedentes familiares." },

            // LOCOMOTOR
            { cat: "Locomotor", q: "Mujer de 68 años con dolor de rodillas que mejora al reposo, rigidez de 10 min y osteofitos. Diagnóstico:", c: "Osteoartrosis (OA)", o: ["Artritis Reumatoide (AR)", "Gota"], j: "El dolor mecánico y la rigidez corta (<30 min) son típicos de la OA." },
            { cat: "Locomotor", q: "¿Cómo se llaman los nódulos en las interfalángicas proximales en la OA?", c: "Nódulos de Bouchard", o: ["Nódulos de Heberden", "Tofos"], j: "Heberden = Distales; Bouchard = Proximales." },
            { cat: "Locomotor", q: "Fármaco de primera elección en OA de rodilla según GPC:", c: "Paracetamol o AINE tópico", o: ["Corticosteroides sistémicos", "Metotrexato"], j: "Se inicia con manejo analgésico simple y medidas no farmacológicas." },
            { cat: "Locomotor", q: "Duración de rigidez matutina para sospechar Artritis Reumatoide:", c: "> 1 hora", o: ["< 30 minutos", "No tiene relación"], j: "La rigidez prolongada refleja la inflamación sinovial sistémica de la AR." },
            { cat: "Locomotor", q: "Marcador con mayor especificidad para Artritis Reumatoide:", c: "Anticuerpos anti-péptido cíclico citrulinado (Anti-CCP)", o: ["Factor Reumatoide", "Proteína C Reactiva"], j: "Los Anti-CCP tienen una especificidad >95% para AR." },
            { cat: "Locomotor", q: "Dosis inicial recomendada de Metotrexato en AR:", c: "7.5 a 15 mg por semana", o: ["25 mg al día", "2.5 mg por semana"], j: "Se inicia con dosis bajas y se escala según tolerancia y respuesta." },
            { cat: "Locomotor", q: "Efecto adverso que requiere suplementación con Ácido Fólico en pacientes que usan MTX:", c: "Estomatitis y toxicidad hematológica", o: ["Gastritis erosiva", "Alopecia areata"], j: "El ácido fólico reduce los efectos secundarios gastrointestinales y de médula ósea del MTX." },
            { cat: "Locomotor", q: "Hallazgo radiográfico avanzado en AR (Erosiones marginales):", c: "Pannus sinovial destruyendo hueso", o: ["Osteofitos", "Esclerosis subcondral"], j: "Las erosiones son el sello distintivo del daño estructural en la AR." },
            { cat: "Locomotor", q: "¿Cuál es el objetivo de la estrategia Treat-to-Target (T2T) en AR?", c: "Lograr remisión clínica o baja actividad de la enfermedad", o: ["Solo eliminar el dolor", "Sustituir todos los FARME por biológicos"], j: "Busca prevenir el daño articular irreversible mediante ajuste estrecho del tratamiento." },

            // PREGUNTAS ADICIONALES PARA DIVERSIDAD (Completando el banco)
            { cat: "Cardiovascular", q: "Criterio de Sokolow-Lyon para HVI:", c: "S en V1 + R en V5/V6 > 35 mm", o: ["R en aVL > 15 mm", "Eje a +90 grados"], j: "Es el criterio de voltaje más validado para hipertrofia ventricular izquierda." },
            { cat: "Urinario", q: "Agente causal más frecuente de IVU no complicada:", c: "Escherichia coli", o: ["Staphylococcus saprophyticus", "Proteus mirabilis"], j: "E. coli causa el 75-90% de los casos." },
            { cat: "Respiratorio", q: "Complicación más grave de la Biopsia Transbronquial:", c: "Neumotórax", o: ["Fiebre", "Tos"], j: "El neumotórax es el riesgo principal y requiere vigilancia radiográfica post-procedimiento." },
            { cat: "Locomotor", q: "Articulación de carga más frecuentemente afectada por OA primaria:", c: "Rodilla (Gonartrosis)", o: ["Cadera (Coxartrosis)", "Tobillo"], j: "La rodilla es el sitio de mayor prevalencia de osteoartrosis clínica." },
            { cat: "Cardiovascular", q: "Principal factor de riesgo para disección aórtica:", c: "Hipertensión Arterial Sistémica", o: ["Tabaquismo", "Dislipidemia"], j: "La HAS crónica debilita la capa media de la aorta facilitando la disección." },
            { cat: "Urinario", q: "Triada de la Prostatitis Crónica / Dolor Pélvico Crónico:", c: "Dolor pélvico, síntomas urinarios y disfunción sexual", o: ["Fiebre, piuria y masa", "Nicturia, hematuria y pérdida de peso"], j: "Suele persistir por >3 meses sin infección bacteriana demostrable." },
            { cat: "Respiratorio", q: "Mecanismo de hipoxemia en la Neumonía:", c: "Cortocircuito (Shunt)", o: ["Espacio muerto", "Hipoventilación alveolar"], j: "Los alvéolos ocupados por exudado están perfundidos pero no ventilados." },
            { cat: "Locomotor", q: "Fármaco biológico anti-TNF utilizado en falla a MTX:", c: "Adalimumab", o: ["Rituximab", "Tocilizumab"], j: "Adalimumab, Infliximab y Etanercept son los anti-TNF clásicos." },
            { cat: "Cardiovascular", q: "Contraindicación absoluta de los IECA:", c: "Embarazo", o: ["Diabetes Mellitus", "Insuficiencia cardiaca"], j: "Son teratogénicos (fetotoxicidad renal)." },
            { cat: "Urinario", q: "Signo de Giordano positivo sugiere:", c: "Pielonefritis aguda", o: ["Cistitis aguda", "Uretritis"], j: "La puño-percusión lumbar dolorosa indica inflamación del parénquima renal." },
            { cat: "Respiratorio", q: "Clasificación GOLD de EPOC con FEV1 30-50%:", c: "GOLD 3 (Grave)", o: ["GOLD 2 (Moderada)", "GOLD 4 (Muy Grave)"], j: "1:≥80%, 2:50-80%, 3:30-50%, 4:<30%." },
            { cat: "Locomotor", q: "Manifestación extraarticular más frecuente en AR:", c: "Nódulos reumatoides", o: ["Uveítis anterior", "Fibrosis pulmonar"], j: "Aparecen en superficies de extensión en pacientes seropositivos." },
            { cat: "Cardiovascular", q: "¿Qué porcentaje de reducción de TA se busca en una Urgencia Hipertensiva?", c: "20-25% en 24 a 48 horas", o: ["100% en 1 hora", "No se debe bajar la TA"], j: "Descensos bruscos pueden causar isquemia cerebral o cardiaca." }
        ];

        let currentQuiz = [];
        let currentIdx = 0;
        let score = 0;
        let userAnswers = [];
        let timerSeconds = 0;
        let timerInterval;

        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        function startQuiz() {
            // Mezclamos todo el banco y seleccionamos 42
            currentQuiz = shuffle([...MASTER_BANK]).slice(0, 42);
            currentIdx = 0;
            score = 0;
            userAnswers = [];
            timerSeconds = 0;
            
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('results-screen').classList.add('hidden');
            document.getElementById('quiz-container').classList.remove('hidden');
            
            startTimer();
            showQuestion();
        }

        function startTimer() {
            if(timerInterval) clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                timerSeconds++;
                const m = String(Math.floor(timerSeconds / 60)).padStart(2, '0');
                const s = String(timerSeconds % 60).padStart(2, '0');
                document.getElementById('timer').innerText = `${m}:${s}`;
            }, 1000);
        }

        function showQuestion() {
            const data = currentQuiz[currentIdx];
            document.getElementById('q-category').innerText = data.cat;
            document.getElementById('q-counter').innerText = `Pregunta ${currentIdx + 1}/42`;
            document.getElementById('question-text').innerText = data.q;
            document.getElementById('progress-bar').style.width = `${(currentIdx / 42) * 100}%`;

            const optionsGrid = document.getElementById('options-grid');
            optionsGrid.innerHTML = '';

            // Generamos las opciones A, B, C mezclando la correcta con las incorrectas
            let options = shuffle([data.c, ...data.o]);
            const labels = ['A', 'B', 'C'];

            options.forEach((opt, i) => {
                const btn = document.createElement('button');
                btn.className = "option-btn w-full text-left p-5 rounded-2xl border-2 border-slate-100 bg-white hover:border-indigo-200 transition-all flex items-center group shadow-sm";
                btn.innerHTML = `
                    <span class="w-10 h-10 rounded-xl bg-slate-50 flex items-center justify-center mr-4 text-slate-400 font-bold group-hover:bg-indigo-50 group-hover:text-indigo-600 transition-colors italic">${labels[i]}</span>
                    <span class="text-slate-700 font-medium">${opt}</span>
                `;
                btn.onclick = () => checkAnswer(opt, data.c, btn);
                optionsGrid.appendChild(btn);
            });

            document.getElementById('btn-next').classList.add('hidden');
        }

        function checkAnswer(selected, correct, btn) {
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

            userAnswers.push({ q: currentQuiz[currentIdx], isCorrect, selected });
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
            clearInterval(timerInterval);
            document.getElementById('quiz-container').classList.add('hidden');
            document.getElementById('results-screen').classList.remove('hidden');

            const percent = Math.round((score / 42) * 100);
            document.getElementById('res-score').innerText = score;
            document.getElementById('res-percent').innerText = `${percent}%`;

            const feedback = document.getElementById('res-feedback');
            if (percent >= 90) feedback.innerText = "¡Nivel Excelente! Dominas las Guías de Práctica Clínica y el razonamiento médico necesario.";
            else if (percent >= 70) feedback.innerText = "Buen desempeño. Estás en el camino correcto, pero revisa los fallos para consolidar.";
            else feedback.innerText = "Necesitas repasar los criterios diagnósticos. Analiza las justificaciones detalladas abajo.";

            const reviewContainer = document.getElementById('detailed-review');
            reviewContainer.innerHTML = '';
            
            userAnswers.forEach((ans, idx) => {
                const card = document.createElement('div');
                card.className = `p-6 rounded-3xl border-l-8 bg-white shadow-sm border ${ans.isCorrect ? 'border-emerald-500' : 'border-rose-500'}`;
                card.innerHTML = `
                    <p class="text-[10px] font-black uppercase text-slate-400 mb-1 tracking-widest">${ans.q.cat}</p>
                    <h4 class="font-bold text-slate-800 mb-3">${idx+1}. ${ans.q.q}</h4>
                    <div class="space-y-2 text-sm">
                        <p><span class="font-bold text-slate-500 uppercase text-[10px]">Tu respuesta:</span> ${ans.selected} ${ans.isCorrect ? '✅' : '❌'}</p>
                        ${!ans.isCorrect ? `<p><span class="font-bold text-emerald-600 uppercase text-[10px]">Correcta:</span> ${ans.q.c}</p>` : ''}
                    </div>
                    <div class="mt-4 p-4 bg-slate-50 rounded-2xl text-xs text-slate-600 leading-relaxed border border-slate-100">
                        <strong class="text-indigo-600 block mb-1 font-bold">JUSTIFICACIÓN CLÍNICA:</strong>
                        ${ans.q.j}
                    </div>
                `;
                reviewContainer.appendChild(card);
            });
        }
    </script>
</body>
</html>
