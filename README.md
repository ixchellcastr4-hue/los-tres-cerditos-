<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>El Gran Presupuesto del Bosque - Simulador Matemático</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* Estilos personalizados para la paleta de colores warm neutral */
        body {
            background-color: #FBF9F4;
            color: #2C3E2E;
            font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
        }
        .bg-wood { background-color: #8B5A2B; }
        .text-wood { color: #8B5A2B; }
        .bg-forest { background-color: #2D5A27; }
        .text-forest { color: #2D5A27; }
        .bg-brick { background-color: #C85A32; }
        .text-brick { color: #C85A32; }
        .bg-straw { background-color: #E6B800; }
        .bg-warm-card { background-color: #F4EFE6; }
        
        /* Contenedor responsivo para Chart.js */
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 380px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 340px;
            }
        }

        /* Efecto de giro para las tarjetas de repaso */
        .perspective-1000 {
            perspective: 1000px;
        }
        .transform-style-3d {
            transform-style: preserve-3d;
            transition: transform 0.6s ease-in-out;
        }
        .backface-hidden {
            backface-visibility: hidden;
        }
        .rotate-y-180 {
            transform: rotateY(180deg);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between">

    <!-- Chosen Palette: Bosque Cálido y Arcilla (#FBF9F4 fondo, #2D5A27 verde bosque, #C85A32 terracota/ladrillo, #8B5A2B madera, #E6B800 paja) -->

    <!-- Application Structure Plan:
        1. Encabezado Interactivo & Estado General: Muestra el título, los roles de los estudiantes y una barra superior con métricas clave (Presupuesto total $100, Gasto, Cambio y Resistencia de la casa).
        2. Navegación por Pestañas Temáticas:
            a) Simulador y Ferretería (Construcción viva en Canvas + prueba del soplo del lobo).
            b) Análisis Estratégico y Gráficos (Comparativas con Chart.js).
            c) Tarjetas de Repaso (Flashcards interactivas basadas en el JSON de práctica).
            d) Hoja de Trabajo del Arquitecto (Verificación de cuentas paso a paso).
            e) Guía Didáctica y Narrativa (Contexto, actos del cuento, objetivos para 4.º de primaria).
        3. Justificación: Esta estructura no lineal permite que los niños pasen inmediatamente del juego práctico de compras a la comprobación matemática analítica y a la ejercitación autónoma.
    -->

    <!-- Visualization & Content Choices:
        - Canvas 2D (Sin SVG): Dibujo interactivo del bosque, la casita según los materiales comprados y animación del soplo del lobo con partículas de viento.
        - Chart.js Donut: Distribución visual del presupuesto (Monedas Gastadas vs Monedas Restantes).
        - Chart.js Bar Chart: Comparativa de costo vs nivel de resistencia entre decisiones predefinidas y la configuración actual.
        - Tarjetas en HTML/CSS 3D: Interacción física tipo memoria/flashcard sin librerías externas complejas.
        - Iconos mediante Unicodes y Canvas (Cumpliendo estrictamente NO SVG / NO Mermaid).
    -->

    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->

    <!-- ENCABEZADO Y BARRA DE ESTADO -->
    <header class="bg-forest text-white shadow-md sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-3 sm:px-6 lg:px-8">
            <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-3">
                <div class="flex items-center space-x-3">
                    <span class="text-3xl" aria-hidden="true">🐷</span>
                    <div>
                        <h1 class="text-xl sm:text-2xl font-bold leading-tight">El Gran Presupuesto del Bosque</h1>
                        <p class="text-xs text-green-200">Asesores Financieros & Arquitectos de 4.º de Primaria</p>
                    </div>
                </div>
                <!-- Indicadores Rápidos -->
                <div class="flex flex-wrap items-center gap-2 text-xs sm:text-sm font-semibold">
                    <div class="bg-white/10 px-3 py-1.5 rounded-lg border border-white/20 flex items-center gap-1.5">
                        <span>💰 Presupuesto:</span>
                        <span id="header-budget" class="text-yellow-300 font-bold">$100</span>
                    </div>
                    <div class="bg-white/10 px-3 py-1.5 rounded-lg border border-white/20 flex items-center gap-1.5">
                        <span>🛒 Gastado:</span>
                        <span id="header-spent" class="text-red-300 font-bold">$0</span>
                    </div>
                    <div class="bg-white/10 px-3 py-1.5 rounded-lg border border-white/20 flex items-center gap-1.5">
                        <span>🪙 Sobrante:</span>
                        <span id="header-remaining" class="text-green-300 font-bold">$100</span>
                    </div>
                    <div class="bg-white/10 px-3 py-1.5 rounded-lg border border-white/20 flex items-center gap-1.5">
                        <span>🛡️ Resistencia:</span>
                        <span id="header-resistance" class="text-blue-200 font-bold">0 pts</span>
                    </div>
                </div>
            </div>
        </div>

        <!-- NAVEGACIÓN PRINCIPAL DE PESTAÑAS -->
        <nav class="bg-emerald-900 border-t border-emerald-800 text-xs sm:text-sm overflow-x-auto">
            <div class="max-w-7xl mx-auto flex space-x-1 px-4">
                <button onclick="switchTab('simulador')" id="tab-simulador" class="py-2.5 px-4 font-medium border-b-2 border-yellow-400 text-yellow-300 whitespace-nowrap focus:outline-none">
                    🛠️ 1. Ferretería y Soplo
                </button>
                <button onclick="switchTab('graficos')" id="tab-graficos" class="py-2.5 px-4 font-medium border-b-2 border-transparent text-emerald-200 hover:text-white whitespace-nowrap focus:outline-none">
                    📊 2. Análisis Gráfico
                </button>
                <button onclick="switchTab('flashcards')" id="tab-flashcards" class="py-2.5 px-4 font-medium border-b-2 border-transparent text-emerald-200 hover:text-white whitespace-nowrap focus:outline-none">
                    🎴 3. Tarjetas de Repaso
                </button>
                <button onclick="switchTab('hoja-calculo')" id="tab-hoja-calculo" class="py-2.5 px-4 font-medium border-b-2 border-transparent text-emerald-200 hover:text-white whitespace-nowrap focus:outline-none">
                    📝 4. Hoja del Arquitecto
                </button>
                <button onclick="switchTab('guia')" id="tab-guia" class="py-2.5 px-4 font-medium border-b-2 border-transparent text-emerald-200 hover:text-white whitespace-nowrap focus:outline-none">
                    📖 5. Guía Narrativa
                </button>
            </div>
        </nav>
    </header>

    <!-- CONTENIDO PRINCIPAL -->
    <main class="max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8 flex-grow w-full">

        <!-- ================= PESTAÑA 1: SIMULADOR Y FERRETERÍA ================= -->
        <section id="sec-simulador" class="space-y-6">
            <!-- Introducción de sección -->
            <div class="bg-amber-50 border-l-4 border-amber-600 p-4 rounded-r-lg shadow-sm">
                <h2 class="text-lg font-bold text-amber-900">¡Bienvenido a la Ferretería del Castor!</h2>
                <p class="text-sm text-amber-800 mt-1">
                    Selecciona los paquetes de materiales para los Tres Cerditos. Recuerda que el Abuelo Cerdito les dejó <strong>$100 monedas de madera</strong> en total. Debes lograr que la casa tenga la resistencia suficiente para soportar los soplos del Lobo Feroz sin excederte del presupuesto.
                </p>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                <!-- Catálogo de Compras (5 cols) -->
                <div class="lg:col-span-5 space-y-4">
                    <h3 class="font-bold text-lg text-forest flex items-center gap-2">
                        <span>🛒 Catálogo de Materiales</span>
                    </h3>

                    <!-- Item 1: Paja -->
                    <div class="bg-white p-4 rounded-xl border border-amber-200 shadow-sm flex items-center justify-between">
                        <div class="flex items-center space-x-3">
                            <span class="text-3xl">📦</span>
                            <div>
                                <h4 class="font-bold text-gray-800">Paquete de Paja</h4>
                                <p class="text-xs text-gray-500">Costo: <strong class="text-amber-600">$10 monedas</strong> | Resistencia: +1</p>
                            </div>
                        </div>
                        <div class="flex items-center space-x-2">
                            <button onclick="changeQty('paja', -1)" class="w-8 h-8 rounded-lg bg-gray-200 hover:bg-gray-300 font-bold text-gray-700">-</button>
                            <span id="qty-paja" class="w-6 text-center font-bold text-gray-800">0</span>
                            <button onclick="changeQty('paja', 1)" class="w-8 h-8 rounded-lg bg-amber-500 hover:bg-amber-600 text-white font-bold">+</button>
                        </div>
                    </div>

                    <!-- Item 2: Madera -->
                    <div class="bg-white p-4 rounded-xl border border-amber-200 shadow-sm flex items-center justify-between">
                        <div class="flex items-center space-x-3">
                            <span class="text-3xl">🪵</span>
                            <div>
                                <h4 class="font-bold text-gray-800">Paquete de Madera</h4>
                                <p class="text-xs text-gray-500">Costo: <strong class="text-amber-600">$25 monedas</strong> | Resistencia: +2</p>
                            </div>
                        </div>
                        <div class="flex items-center space-x-2">
                            <button onclick="changeQty('madera', -1)" class="w-8 h-8 rounded-lg bg-gray-200 hover:bg-gray-300 font-bold text-gray-700">-</button>
                            <span id="qty-madera" class="w-6 text-center font-bold text-gray-800">0</span>
                            <button onclick="changeQty('madera', 1)" class="w-8 h-8 rounded-lg bg-amber-700 hover:bg-amber-800 text-white font-bold">+</button>
                        </div>
                    </div>

                    <!-- Item 3: Ladrillo -->
                    <div class="bg-white p-4 rounded-xl border border-amber-200 shadow-sm flex items-center justify-between">
                        <div class="flex items-center space-x-3">
                            <span class="text-3xl">🧱</span>
                            <div>
                                <h4 class="font-bold text-gray-800">Paquete de Ladrillos</h4>
                                <p class="text-xs text-gray-500">Costo: <strong class="text-amber-600">$40 monedas</strong> | Resistencia: +3</p>
                            </div>
                        </div>
                        <div class="flex items-center space-x-2">
                            <button onclick="changeQty('ladrillos', -1)" class="w-8 h-8 rounded-lg bg-gray-200 hover:bg-gray-300 font-bold text-gray-700">-</button>
                            <span id="qty-ladrillos" class="w-6 text-center font-bold text-gray-800">0</span>
                            <button onclick="changeQty('ladrillos', 1)" class="w-8 h-8 rounded-lg bg-brick hover:bg-red-700 text-white font-bold">+</button>
                        </div>
                    </div>

                    <!-- Item 4: Puerta de Hierro -->
                    <div class="bg-white p-4 rounded-xl border border-amber-200 shadow-sm flex items-center justify-between">
                        <div class="flex items-center space-x-3">
                            <span class="text-3xl">🚪</span>
                            <div>
                                <h4 class="font-bold text-gray-800">Puerta Reforzada</h4>
                                <p class="text-xs text-gray-500">Costo: <strong class="text-amber-600">$15 monedas</strong> | Resistencia: +2 extra</p>
                            </div>
                        </div>
                        <div class="flex items-center space-x-2">
                            <button onclick="changeQty('puerta', -1)" class="w-8 h-8 rounded-lg bg-gray-200 hover:bg-gray-300 font-bold text-gray-700">-</button>
                            <span id="qty-puerta" class="w-6 text-center font-bold text-gray-800">0</span>
                            <button onclick="changeQty('puerta', 1)" class="w-8 h-8 rounded-lg bg-slate-700 hover:bg-slate-800 text-white font-bold">+</button>
                        </div>
                    </div>

                    <!-- Resumen del Carrito y Botón Reset -->
                    <div class="bg-warm-card p-4 rounded-xl border border-amber-300 space-y-2">
                        <div class="flex justify-between text-sm">
                            <span>Suma Total Compras:</span>
                            <span id="summary-total" class="font-bold text-brick">$0 monedas</span>
                        </div>
                        <div class="flex justify-between text-sm">
                            <span>Dinero Sobrante (Cambio):</span>
                            <span id="summary-remaining" class="font-bold text-forest">$100 monedas</span>
                        </div>
                        <div id="budget-warning" class="hidden text-xs text-red-600 font-bold bg-red-100 p-2 rounded">
                            ⚠️ ¡Cuidado! Te has pasado del presupuesto de $100 monedas. Ajusta tus compras.
                        </div>
                        <button onclick="resetCart()" class="w-full mt-2 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 font-semibold text-xs rounded-lg transition">
                            🔄 Vaciar Carrito y Empezar de Nuevo
                        </button>
                    </div>
                </div>

                <!-- Visor Canvas del Bosque y Simulador del Soplo (7 cols) -->
                <div class="lg:col-span-7 bg-white p-4 rounded-2xl border border-amber-200 shadow-sm flex flex-col justify-between">
                    <div>
                        <div class="flex justify-between items-center mb-3">
                            <h3 class="font-bold text-lg text-forest flex items-center gap-2">
                                <span>🏠 Construcción de la Casita</span>
                            </h3>
                            <span id="house-status-badge" class="px-3 py-1 rounded-full text-xs font-bold bg-amber-100 text-amber-800">
                                Sin Materiales
                            </span>
                        </div>
                        <p class="text-xs text-gray-500 mb-2">
                            Observa cómo se construye la casa en tiempo real. Presiona el botón para simular el ataque del Lobo Feroz (se requieren al menos 5 puntos de resistencia para soportar la fuerza del viento).
                        </p>

                        <!-- Canvas de Representación Visual -->
                        <div class="relative w-full bg-sky-100 rounded-xl overflow-hidden border border-sky-200 shadow-inner flex justify-center items-center" style="height: 280px;">
                            <canvas id="canvas-house" width="550" height="280" class="w-full h-full object-contain"></canvas>
                        </div>
                    </div>

                    <!-- Botón de Simulación del Lobo -->
                    <div class="mt-4 pt-3 border-t border-gray-100 flex flex-col sm:flex-row items-center justify-between gap-3">
                        <div class="text-xs text-gray-600">
                            Puntos acumulados: <strong id="canvas-res-pts" class="text-base text-forest">0</strong> pts
                        </div>
                        <button onclick="testWolfBlow()" id="btn-wolf-blow" class="w-full sm:w-auto px-6 py-2.5 bg-brick hover:bg-red-700 text-white font-bold text-sm rounded-xl shadow-md flex items-center justify-center gap-2 transition transform active:scale-95">
                            <span>🐺</span> ¡Probar Soplo del Lobo! 💨
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- ================= PESTAÑA 2: ANÁLISIS GRÁFICO ================= -->
        <section id="sec-graficos" class="hidden space-y-6">
            <div class="bg-emerald-50 border-l-4 border-emerald-600 p-4 rounded-r-lg shadow-sm">
                <h2 class="text-lg font-bold text-emerald-900">Análisis Matemático & Estratégico de Compras</h2>
                <p class="text-sm text-emerald-800 mt-1">
                    Compara visualmente cómo distribuyes tu presupuesto y analiza las diferentes combinaciones estratégicas de construcción para entender la relación entre costo y seguridad.
                </p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- Gráfico 1: Distribución del Presupuesto -->
                <div class="bg-white p-5 rounded-2xl border border-gray-200 shadow-sm flex flex-col items-center">
                    <h3 class="font-bold text-base text-gray-800 mb-1 w-full text-left">📊 Distribución Actual del Presupuesto</h3>
                    <p class="text-xs text-gray-500 mb-4 w-full text-left">Desglose porcentual entre compras y monedas restantes.</p>
                    <div class="chart-container">
                        <canvas id="chart-budget"></canvas>
                    </div>
                    <p id="chart-budget-desc" class="text-xs text-center text-gray-600 mt-3 bg-warm-card p-2 rounded-lg w-full">
                        Cargando estado del presupuesto...
                    </p>
                </div>

                <!-- Gráfico 2: Comparador de Estrategias -->
                <div class="bg-white p-5 rounded-2xl border border-gray-200 shadow-sm flex flex-col items-center">
                    <h3 class="font-bold text-base text-gray-800 mb-1 w-full text-left">📈 Comparativa de Estrategias vs Resistencia</h3>
                    <p class="text-xs text-gray-500 mb-4 w-full text-left">Estrategias típicas frente a tu presupuesto actual.</p>
                    <div class="chart-container">
                        <canvas id="chart-comparison"></canvas>
                    </div>
                    <p class="text-xs text-center text-gray-600 mt-3 bg-warm-card p-2 rounded-lg w-full">
                        La estrategia óptima equilibra una resistencia ≥ 5 puntos sin agotar la totalidad del presupuesto.
                    </p>
                </div>
            </div>
        </section>

        <!-- ================= PESTAÑA 3: TARJETAS DE REPASO ================= -->
        <section id="sec-flashcards" class="hidden space-y-6">
            <div class="bg-amber-50 border-l-4 border-amber-500 p-4 rounded-r-lg shadow-sm">
                <h2 class="text-lg font-bold text-amber-900">🎴 Tarjetas de Repaso Matemático</h2>
                <p class="text-sm text-amber-800 mt-1">
                    Pon a prueba tus habilidades de cálculo rápido, resolución de multiplicaciones y conceptos clave del presupuesto. Haz clic en cada tarjeta para girarla y revelar la respuesta correcta.
                </p>
            </div>

            <!-- Controles de Filtro y Contador -->
            <div class="flex flex-col sm:flex-row justify-between items-center gap-4 bg-white p-4 rounded-xl border border-gray-200 shadow-sm">
                <div class="flex items-center space-x-2 w-full sm:w-auto">
                    <span class="text-xs font-bold text-gray-600">Filtrar por:</span>
                    <select id="flashcard-filter" onchange="filterFlashcards()" class="bg-warm-card text-xs font-semibold px-3 py-2 rounded-lg border border-gray-300 focus:outline-none">
                        <option value="ALL">Todas las tarjetas (25)</option>
                        <option value="PRECIOS">Precios y Materiales</option>
                        <option value="OPERACIONES">Cálculos de Total y Cambio</option>
                        <option value="CONCEPTOS">Conceptos Matemáticos</option>
                    </select>
                </div>
                <div class="text-xs font-semibold text-forest bg-green-100 px-3 py-1.5 rounded-full">
                    Tarjeta <span id="fc-current-index">1</span> de <span id="fc-total-count">25</span>
                </div>
            </div>

            <!-- Visualizador de Flashcard Individual -->
            <div class="max-w-xl mx-auto">
                <div class="perspective-1000 w-full h-72">
                    <div id="flashcard-inner" onclick="flipFlashcard()" class="relative w-full h-full transform-style-3d cursor-pointer rounded-2xl shadow-lg border-2 border-amber-200 bg-white hover:border-amber-400 transition-all">
                        <!-- Frente -->
                        <div class="absolute inset-0 w-full h-full backface-hidden p-6 flex flex-col justify-between bg-gradient-to-br from-white to-amber-50 rounded-2xl">
                            <div class="flex justify-between items-center">
                                <span class="text-xs font-bold px-2.5 py-1 bg-amber-200 text-amber-900 rounded-md">Pregunta</span>
                                <span class="text-xs text-gray-400">👆 Toca para girar</span>
                            </div>
                            <div class="my-auto text-center">
                                <h3 id="fc-front-text" class="text-lg sm:text-xl font-bold text-gray-800">Cargando pregunta...</h3>
                                <p id="fc-hint-text" class="text-xs text-amber-700 mt-2 font-medium bg-amber-100/50 py-1 px-3 rounded-full inline-block">Pista disponible...</p>
                            </div>
                            <div class="text-right">
                                <span class="text-2xl">🐷</span>
                            </div>
                        </div>

                        <!-- Reverso -->
                        <div class="absolute inset-0 w-full h-full backface-hidden rotate-y-180 p-6 flex flex-col justify-between bg-forest text-white rounded-2xl">
                            <div class="flex justify-between items-center">
                                <span class="text-xs font-bold px-2.5 py-1 bg-emerald-800 text-emerald-100 rounded-md">Respuesta Correcta</span>
                                <span class="text-xs text-emerald-200">✨ Solución</span>
                            </div>
                            <div class="my-auto text-center">
                                <h3 id="fc-back-text" class="text-xl sm:text-2xl font-bold text-yellow-300">Respuesta...</h3>
                            </div>
                            <div class="text-right">
                                <span class="text-2xl">🧮</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Botones de Navegación del Mazo -->
                <div class="flex justify-between items-center mt-6">
                    <button onclick="prevFlashcard()" class="px-5 py-2.5 bg-white hover:bg-gray-100 border border-gray-300 rounded-xl text-xs font-bold text-gray-700 shadow-sm transition">
                        ⬅️ Anterior
                    </button>
                    <button onclick="flipFlashcard()" class="px-5 py-2.5 bg-amber-500 hover:bg-amber-600 text-white rounded-xl text-xs font-bold shadow-md transition">
                        🔄 Girar Tarjeta
                    </button>
                    <button onclick="nextFlashcard()" class="px-5 py-2.5 bg-white hover:bg-gray-100 border border-gray-300 rounded-xl text-xs font-bold text-gray-700 shadow-sm transition">
                        Siguiente ➡️
                    </button>
                </div>
            </div>
        </section>

        <!-- ================= PESTAÑA 4: HOJA DEL ARQUITECTO ================= -->
        <section id="sec-hoja-calculo" class="hidden space-y-6">
            <div class="bg-blue-50 border-l-4 border-blue-600 p-4 rounded-r-lg shadow-sm">
                <h2 class="text-lg font-bold text-blue-900">📝 Hoja Oficial del Arquitecto del Bosque</h2>
                <p class="text-sm text-blue-800 mt-1">
                    Registra las operaciones matemáticas de tus compras para solicitar la aprobación del <strong>Tiendero Castor</strong>. Revisa tus multiplicaciones, sumas y restas antes de enviar.
                </p>
            </div>

            <div class="bg-white p-6 rounded-2xl border border-gray-200 shadow-sm space-y-6">
                <!-- Datos del Estudiante -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 pb-4 border-b border-gray-200">
                    <div>
                        <label class="block text-xs font-bold text-gray-600 mb-1">Nombre / Equipo:</label>
                        <input type="text" id="ws-student-name" placeholder="Ej. Los Asesores del Bosque" class="w-full bg-warm-card border border-gray-300 rounded-lg p-2 text-sm font-semibold focus:outline-none focus:border-forest">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-gray-600 mb-1">Presupuesto Inicial:</label>
                        <input type="text" value="$100 Monedas de Madera" disabled class="w-full bg-gray-100 border border-gray-300 rounded-lg p-2 text-sm font-bold text-amber-700">
                    </div>
                </div>

                <!-- Tabla de Multiplicaciones de Compras -->
                <div>
                    <h3 class="font-bold text-sm text-gray-800 mb-3">1. Registro y Cálculo de Costos Unitarios</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs sm:text-sm border-collapse">
                            <thead>
                                <tr class="bg-warm-card text-gray-700 border-b border-gray-300">
                                    <th class="p-3">Material</th>
                                    <th class="p-3 text-center">Precio Unitario</th>
                                    <th class="p-3 text-center">Cantidad</th>
                                    <th class="p-3 text-center">Operación Sugerida</th>
                                    <th class="p-3 text-center">Tu Cálculo Total ($)</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-200">
                                <tr>
                                    <td class="p-3 font-semibold">📦 Paquete de Paja</td>
                                    <td class="p-3 text-center">$10</td>
                                    <td class="p-3 text-center" id="ws-qty-paja">0</td>
                                    <td class="p-3 text-center text-gray-500" id="ws-op-paja">10 × 0</td>
                                    <td class="p-3 text-center">
                                        <input type="number" id="ws-val-paja" placeholder="0" class="w-20 text-center bg-warm-card border border-gray-300 rounded p-1 font-bold">
                                    </td>
                                </tr>
                                <tr>
                                    <td class="p-3 font-semibold">🪵 Paquete de Madera</td>
                                    <td class="p-3 text-center">$25</td>
                                    <td class="p-3 text-center" id="ws-qty-madera">0</td>
                                    <td class="p-3 text-center text-gray-500" id="ws-op-madera">25 × 0</td>
                                    <td class="p-3 text-center">
                                        <input type="number" id="ws-val-madera" placeholder="0" class="w-20 text-center bg-warm-card border border-gray-300 rounded p-1 font-bold">
                                    </td>
                                </tr>
                                <tr>
                                    <td class="p-3 font-semibold">🧱 Paquete de Ladrillos</td>
                                    <td class="p-3 text-center">$40</td>
                                    <td class="p-3 text-center" id="ws-qty-ladrillos">0</td>
                                    <td class="p-3 text-center text-gray-500" id="ws-op-ladrillos">40 × 0</td>
                                    <td class="p-3 text-center">
                                        <input type="number" id="ws-val-ladrillos" placeholder="0" class="w-20 text-center bg-warm-card border border-gray-300 rounded p-1 font-bold">
                                    </td>
                                </tr>
                                <tr>
                                    <td class="p-3 font-semibold">🚪 Puerta de Hierro</td>
                                    <td class="p-3 text-center">$15</td>
                                    <td class="p-3 text-center" id="ws-qty-puerta">0</td>
                                    <td class="p-3 text-center text-gray-500" id="ws-op-puerta">15 × 0</td>
                                    <td class="p-3 text-center">
                                        <input type="number" id="ws-val-puerta" placeholder="0" class="w-20 text-center bg-warm-card border border-gray-300 rounded p-1 font-bold">
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <!-- Sección de Operaciones Globales (Suma y Resta) -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6 pt-4 border-t border-gray-200">
                    <!-- Suma Total -->
                    <div class="bg-amber-50 p-4 rounded-xl border border-amber-200 space-y-2">
                        <h4 class="font-bold text-xs text-amber-900">A. ¿Cuánto dinero gastamos en total? (Suma)</h4>
                        <p class="text-xs text-gray-600">Paja + Madera + Ladrillos + Puerta</p>
                        <div class="flex items-center space-x-2">
                            <span class="text-sm font-bold">$</span>
                            <input type="number" id="ws-user-total" placeholder="Escribe la suma" class="w-full bg-white border border-amber-300 rounded p-2 text-sm font-bold">
                        </div>
                    </div>

                    <!-- Resta / Cambio -->
                    <div class="bg-green-50 p-4 rounded-xl border border-green-200 space-y-2">
                        <h4 class="font-bold text-xs text-green-900">B. ¿Cuánto dinero nos sobra? (Resta)</h4>
                        <p class="text-xs text-gray-600">Cambio = 100 - Gasto Total</p>
                        <div class="flex items-center space-x-2">
                            <span class="text-sm font-bold">$</span>
                            <input type="number" id="ws-user-change" placeholder="Escribe el cambio" class="w-full bg-white border border-green-300 rounded p-2 text-sm font-bold">
                        </div>
                    </div>
                </div>

                <!-- Justificación Estratégica -->
                <div>
                    <label class="block text-xs font-bold text-gray-700 mb-1">3. Decisión del Equipo:</label>
                    <textarea id="ws-justification" rows="2" placeholder="Explica brevemente por qué eligieron estos materiales para proteger a los cerditos..." class="w-full bg-warm-card border border-gray-300 rounded-lg p-3 text-xs focus:outline-none focus:border-forest"></textarea>
                </div>

                <!-- Botón de Verificación del Tiendero Castor -->
                <div class="pt-2 flex flex-col sm:flex-row items-center justify-between gap-4">
                    <button onclick="validateWorksheet()" class="w-full sm:w-auto px-6 py-3 bg-forest hover:bg-emerald-800 text-white font-bold text-sm rounded-xl shadow-md transition">
                        🦫 Revisar Cuentas con el Tiendero Castor
                    </button>
                    <div id="ws-feedback" class="hidden w-full sm:w-auto px-4 py-2 rounded-lg text-xs font-bold"></div>
                </div>
            </div>
        </section>

        <!-- ================= PESTAÑA 5: GUÍA NARRATIVA ================= -->
        <section id="sec-guia" class="hidden space-y-6">
            <div class="bg-stone-100 border-l-4 border-stone-600 p-4 rounded-r-lg shadow-sm">
                <h2 class="text-lg font-bold text-stone-900">📖 Marco Narrativo y Objetivos de Aprendizaje</h2>
                <p class="text-sm text-stone-700 mt-1">
                    Revisión de la secuencia didáctica para 4.º de primaria: personajes, reglas del mundo y estructura de la historia.
                </p>
            </div>

            <!-- Ficha Didáctica resumida -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <!-- Objetivos -->
                <div class="bg-white p-5 rounded-2xl border border-gray-200 shadow-sm space-y-3">
                    <h3 class="font-bold text-base text-forest flex items-center gap-2">
                        <span>🎯 Objetivos de Aprendizaje</span>
                    </h3>
                    <ul class="text-xs text-gray-600 space-y-2 list-disc list-inside">
                        <td><strong>Resolver problemas cotidianos:</strong> Operaciones de suma, resta y multiplicación por una cifra.</td>
                        <td><strong>Administrar presupuesto:</strong> Gestionar estratégicamente 100 monedas de madera.</td>
                        <td><strong>Justificación matemática:</strong> Argumentar la toma de decisiones basada en cálculos sencillos.</td>
                    </ul>
                </div>

                <!-- Personajes -->
                <div class="bg-white p-5 rounded-2xl border border-gray-200 shadow-sm space-y-3">
                    <h3 class="font-bold text-base text-brick flex items-center gap-2">
                        <span>🐷 Personajes Clave</span>
                    </h3>
                    <ul class="text-xs text-gray-600 space-y-2">
                        <li><strong>Tontín (Menor):</strong> Quiere gastar poco en paja ($10) para comprar caramelos.</li>
                        <li><strong>Trabajador (Mediano):</strong> Prefiere madera ($25) sin calcular paquetes exactos.</li>
                        <li><strong>Prudente (Mayor):</strong> Desea ladrillos ($40) y busca apoyo financiero en la clase.</li>
                        <li><strong>Tiendero Castor:</strong> Encargado de verificar las cuentas en la ferretería.</li>
                    </ul>
                </div>

                <!-- Reglas del Mundo -->
                <div class="bg-white p-5 rounded-2xl border border-gray-200 shadow-sm space-y-3">
                    <h3 class="font-bold text-base text-wood flex items-center gap-2">
                        <span>📜 Reglas del Bosque</span>
                    </h3>
                    <ol class="text-xs text-gray-600 space-y-2 list-decimal list-inside">
                        <li>Cada paquete tiene un costo fijo e inamovible en monedas.</li>
                        <li>No existe el fiado; el límite máximo es $100 monedas.</li>
                        <li>Toda compra debe quedar asentada en la Hoja del Arquitecto.</li>
                    </ol>
                </div>
            </div>

            <!-- Desarrollo de los Actos -->
            <div class="bg-white p-6 rounded-2xl border border-gray-200 shadow-sm space-y-4">
                <h3 class="font-bold text-base text-gray-800">📜 Desarrollo de la Historia (3 Actos)</h3>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-xs">
                    <div class="bg-amber-50/50 p-4 rounded-xl border border-amber-200">
                        <h4 class="font-bold text-amber-900 mb-1">Acto I: El Legado</h4>
                        <p class="text-gray-600">Los cerditos heredan 100 monedas de madera del Abuelo Cerdito. Ante la duda de cómo gastarlo, acuden a los mejores matemáticos de 4.º grado.</p>
                    </div>
                    <div class="bg-emerald-50/50 p-4 rounded-xl border border-emerald-200">
                        <h4 class="font-bold text-emerald-900 mb-1">Acto II: La Ferretería</h4>
                        <p class="text-gray-600">Los estudiantes revisan los precios del Castor (Paja $10, Madera $25, Ladrillo $40, Puerta $15) y planifican sus compras.</p>
                    </div>
                    <div class="bg-red-50/50 p-4 rounded-xl border border-red-200">
                        <h4 class="font-bold text-red-900 mb-1">Acto III: El Soplo</h4>
                        <p class="text-gray-600">Se comprueban los cálculos. Si las cuentas son correctas y la casa es firme, ¡el Lobo Feroz no podrá derribar el refugio!</p>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- PIE DE PÁGINA -->
    <footer class="bg-forest text-white text-xs py-4 mt-8">
        <div class="max-w-7xl mx-auto px-4 text-center space-y-1">
            <p class="font-semibold">El Gran Presupuesto del Bosque — Unidad Didáctica de Matemáticas (4.º Primaria)</p>
            <p class="text-emerald-200">Desarrollado para el aprendizaje interactivo de operaciones básicas y educación financiera escolar.</p>
        </div>
    </footer>

    <!-- LÓGICA JAVASCRIPT VANILLA -->
    <script>
        // ================= ESTADO GLOBAL DE LA APLICACIÓN =================
        const state = {
            cart: {
                paja: 0,
                madera: 0,
                ladrillos: 0,
                puerta: 0
            },
            prices: {
                paja: 10,
                madera: 25,
                ladrillos: 40,
                puerta: 15
            },
            resistance: {
                paja: 1,
                madera: 2,
                ladrillos: 3,
                puerta: 2
            },
            initialBudget: 100,
            activeTab: 'simulador'
        };

        // Datos de Flashcards desde el JSON original
        const flashcardsData = [
            { front: "Costo de 1 paquete de paja", back: "$10 monedas", hint: "Es el material más barato del catálogo.", cat: "PRECIOS" },
            { front: "Costo de 1 paquete de madera", back: "$25 monedas", hint: "Tiene un precio intermedio.", cat: "PRECIOS" },
            { front: "Costo de 1 paquete de ladrillos", back: "$40 monedas", hint: "Es el material con mayor resistencia.", cat: "PRECIOS" },
            { front: "Costo de 1 puerta de hierro reforzada", back: "$15 monedas", hint: "Accesorio de protección extra.", cat: "PRECIOS" },
            { front: "Costo total de 3 paquetes de paja", back: "$30 monedas (10 × 3 = 30)", hint: "Multiplica el valor unitario por tres.", cat: "OPERACIONES" },
            { front: "Costo total de 2 paquetes de madera", back: "$50 monedas (25 × 2 = 50)", hint: "Suma 25 + 25 o multiplica por dos.", cat: "OPERACIONES" },
            { front: "Costo total de 2 paquetes de ladrillos", back: "$80 monedas (40 × 2 = 80)", hint: "Calcula el doble de 40.", cat: "OPERACIONES" },
            { front: "Costo total: 1 paquete de ladrillos + 1 paquete de paja", back: "$50 monedas (40 + 10 = 50)", hint: "Suma 40 y 10.", cat: "OPERACIONES" },
            { front: "Costo total: 1 paquete de madera + 1 paquete de paja", back: "$35 monedas (25 + 10 = 35)", hint: "Suma 25 y 10.", cat: "OPERACIONES" },
            { front: "Cálculo de cambio: presupuesto $100 y gasto de $80", back: "$20 monedas (100 - 80 = 20)", hint: "Resta el gasto total al dinero inicial.", cat: "OPERACIONES" },
            { front: "Cálculo de cambio: presupuesto $100 y gasto de $95", back: "$5 monedas (100 - 95 = 5)", hint: "Resta 95 a 100.", cat: "OPERACIONES" },
            { front: "Cálculo de cambio: presupuesto $100 y gasto de $60", back: "$40 monedas (100 - 60 = 40)", hint: "Resta 60 a 100.", cat: "OPERACIONES" },
            { front: "Definición de Presupuesto", back: "Cantidad de dinero disponible para planificar compras y controlar gastos.", hint: "Plan de dinero antes de gastar.", cat: "CONCEPTOS" },
            { front: "Fórmula para calcular el dinero restante (cambio)", back: "Cambio = Presupuesto Inicial - Gasto Total", hint: "Restar lo gastado de lo que teníamos al inicio.", cat: "CONCEPTOS" },
            { front: "Costo total de 4 paquetes de paja", back: "$40 monedas (10 × 4 = 40)", hint: "Cuatro veces diez.", cat: "OPERACIONES" },
            { front: "Costo total de 3 paquetes de madera", back: "$75 monedas (25 × 3 = 75)", hint: "Tres veces veinticinco.", cat: "OPERACIONES" },
            { front: "Material de construcción con mayor resistencia", back: "Los ladrillos", hint: "Resiste los soplos más fuertes del lobo.", cat: "CONCEPTOS" },
            { front: "Material de construcción con menor resistencia", back: "La paja", hint: "Se cae con el primer soplo.", cat: "CONCEPTOS" },
            { front: "Presupuesto total entregado a los Cerditos", back: "$100 monedas de madera", hint: "Monto inicial del Abuelo Cerdito.", cat: "CONCEPTOS" },
            { front: "Costo total: 1 paquete de ladrillos + 1 puerta de hierro", back: "$55 monedas (40 + 15 = 55)", hint: "Suma 40 y 15.", cat: "OPERACIONES" },
            { front: "Cambio sobrante al gastar $55 de un presupuesto de $100", back: "$45 monedas (100 - 55 = 45)", hint: "Resta 55 a 100.", cat: "OPERACIONES" },
            { front: "Operación útil para calcular varios paquetes del mismo precio", back: "La multiplicación", hint: "Abreviatura de una suma repetida.", cat: "CONCEPTOS" },
            { front: "Operación matemática para calcular el costo total de la compra", back: "La suma o adición", hint: "Permite juntar los precios de todos los materiales.", cat: "CONCEPTOS" },
            { front: "Operación matemática para calcular el dinero que nos deben devolver", back: "La resta o sustracción", hint: "Permite encontrar la diferencia entre el dinero dado y el costo.", cat: "CONCEPTOS" },
            { front: "Costo total: 2 paquetes de paja + 1 paquete de madera", back: "$45 monedas (20 + 25 = 45)", hint: "Suma 20 de la paja y 25 de la madera.", cat: "OPERACIONES" }
        ];

        let filteredFC = [...flashcardsData];
        let currentFCIndex = 0;
        let isFlipped = false;

        // Instancias de Gráficos Chart.js
        let chartBudgetObj = null;
        let chartComparisonObj = null;

        // Variables de Animación de Canvas
        let wolfBlowing = false;
        let wolfAnimProgress = 0;

        // ================= GESTIÓN DE PESTAÑAS =================
        function switchTab(tabId) {
            state.activeTab = tabId;
            const tabs = ['simulador', 'graficos', 'flashcards', 'hoja-calculo', 'guia'];
            tabs.forEach(t => {
                const btn = document.getElementById(`tab-${t}`);
                const sec = document.getElementById(`sec-${t}`);
                if (t === tabId) {
                    sec.classList.remove('hidden');
                    btn.classList.add('border-yellow-400', 'text-yellow-300');
                    btn.classList.remove('border-transparent', 'text-emerald-200');
                } else {
                    sec.classList.add('hidden');
                    btn.classList.remove('border-yellow-400', 'text-yellow-300');
                    btn.classList.add('border-transparent', 'text-emerald-200');
                }
            });

            if (tabId === 'graficos') {
                renderCharts();
            } else if (tabId === 'hoja-calculo') {
                updateWorksheetView();
            }
        }

        // ================= LÓGICA DE COMPRAS / FERRETERÍA =================
        function changeQty(item, delta) {
            const current = state.cart[item];
            const next = current + delta;
            if (next >= 0) {
                state.cart[item] = next;
                document.getElementById(`qty-${item}`).innerText = next;
                updateTotals();
            }
        }

        function resetCart() {
            state.cart = { paja: 0, madera: 0, ladrillos: 0, puerta: 0 };
            ['paja', 'madera', 'ladrillos', 'puerta'].forEach(i => {
                document.getElementById(`qty-${i}`).innerText = '0';
            });
            updateTotals();
        }

        function calculateCartMetrics() {
            const spent = (state.cart.paja * state.prices.paja) +
                          (state.cart.madera * state.prices.madera) +
                          (state.cart.ladrillos * state.prices.ladrillos) +
                          (state.cart.puerta * state.prices.puerta);
            
            const remaining = state.initialBudget - spent;
            
            const resPts = (state.cart.paja * state.resistance.paja) +
                           (state.cart.madera * state.resistance.madera) +
                           (state.cart.ladrillos * state.resistance.ladrillos) +
                           (state.cart.puerta * state.resistance.puerta);

            return { spent, remaining, resPts };
        }

        function updateTotals() {
            const { spent, remaining, resPts } = calculateCartMetrics();

            // Actualizar Encabezado
            document.getElementById('header-spent').innerText = `$${spent}`;
            document.getElementById('header-remaining').innerText = `$${remaining}`;
            document.getElementById('header-resistance').innerText = `${resPts} pts`;

            // Actualizar Resumen
            document.getElementById('summary-total').innerText = `$${spent} monedas`;
            document.getElementById('summary-remaining').innerText = `$${remaining} monedas`;
            document.getElementById('canvas-res-pts').innerText = resPts;

            // Alerta de presupuesto
            const warning = document.getElementById('budget-warning');
            if (remaining < 0) {
                warning.classList.remove('hidden');
            } else {
                warning.classList.add('hidden');
            }

            // Badge de Estado de Casa
            const badge = document.getElementById('house-status-badge');
            if (resPts === 0) {
                badge.innerText = 'Sin Materiales';
                badge.className = 'px-3 py-1 rounded-full text-xs font-bold bg-gray-100 text-gray-700';
            } else if (resPts < 5) {
                badge.innerText = 'Resistencia Débil ⚠️';
                badge.className = 'px-3 py-1 rounded-full text-xs font-bold bg-amber-100 text-amber-800';
            } else {
                badge.innerText = '¡Casa Muy Segura! 🛡️';
                badge.className = 'px-3 py-1 rounded-full text-xs font-bold bg-green-100 text-green-800';
            }

            drawHouseCanvas();
            if (state.activeTab === 'graficos') renderCharts();
        }

        // ================= DIBUJO EN CANVAS DE LA CASA & ANIMACIÓN =================
        function drawHouseCanvas() {
            const canvas = document.getElementById('canvas-house');
            if (!canvas) return;
            const ctx = canvas.getContext('2d');
            const w = canvas.width;
            const h = canvas.height;

            // Limpiar fondo (Cielo)
            ctx.fillStyle = '#E0F2FE';
            ctx.fillRect(0, 0, w, h);

            // Suelo de Hierba
            ctx.fillStyle = '#4ADE80';
            ctx.fillRect(0, h - 60, w, 60);
            ctx.fillStyle = '#16A34A';
            ctx.fillRect(0, h - 50, w, 10);

            // Sol y nubes
            ctx.fillStyle = '#FDE047';
            ctx.beginPath();
            ctx.arc(480, 50, 25, 0, Math.PI * 2);
            ctx.fill();

            const { resPts } = calculateCartMetrics();

            // Dibujar estructura de la casa si hay materiales
            const houseX = 220;
            const houseY = h - 160;
            const houseW = 160;
            const houseH = 100;

            if (resPts > 0) {
                // Sombra de la casa
                ctx.fillStyle = 'rgba(0,0,0,0.15)';
                ctx.beginPath();
                ctx.ellipse(houseX + houseW/2, h - 20, houseW/1.5, 12, 0, 0, Math.PI * 2);
                ctx.fill();

                // Paredes según el material predominante
                if (state.cart.ladrillos > 0) {
                    ctx.fillStyle = '#C85A32'; // Ladrillo
                    ctx.fillRect(houseX, houseY, houseW, houseH);
                    // Textura de ladrillos
                    ctx.strokeStyle = '#9A3412';
                    ctx.lineWidth = 1.5;
                    for(let y = houseY; y < houseY + houseH; y += 15) {
                        ctx.beginPath(); ctx.moveTo(houseX, y); ctx.lineTo(houseX + houseW, y); ctx.stroke();
                    }
                } else if (state.cart.madera > 0) {
                    ctx.fillStyle = '#8B5A2B'; // Madera
                    ctx.fillRect(houseX, houseY, houseW, houseH);
                    // Textura de tablas de madera
                    ctx.strokeStyle = '#583111';
                    ctx.lineWidth = 2;
                    for(let x = houseX; x < houseX + houseW; x += 20) {
                        ctx.beginPath(); ctx.moveTo(x, houseY); ctx.lineTo(x, houseY + houseH); ctx.stroke();
                    }
                } else {
                    ctx.fillStyle = '#E6B800'; // Paja
                    ctx.fillRect(houseX, houseY, houseW, houseH);
                }

                // Techo
                ctx.fillStyle = state.cart.ladrillos > 0 ? '#7F1D1D' : (state.cart.madera > 0 ? '#583111' : '#CA8A04');
                ctx.beginPath();
                ctx.moveTo(houseX - 20, houseY);
                ctx.lineTo(houseX + houseW / 2, houseY - 60);
                ctx.lineTo(houseX + houseW + 20, houseY);
                ctx.closePath();
                ctx.fill();

                // Puerta
                const doorW = 40;
                const doorH = 60;
                const doorX = houseX + (houseW - doorW) / 2;
                const doorY = houseY + houseH - doorH;

                if (state.cart.puerta > 0) {
                    ctx.fillStyle = '#334155'; // Puerta de Hierro
                    ctx.fillRect(doorX, doorY, doorW, doorH);
                    ctx.fillStyle = '#F59E0B'; // Pomo dorado
                    ctx.beginPath();
                    ctx.arc(doorX + 8, doorY + doorH/2, 4, 0, Math.PI*2);
                    ctx.fill();
                } else {
                    ctx.fillStyle = '#64748B'; // Puerta básica
                    ctx.fillRect(doorX, doorY, doorW, doorH);
                }

                // Cerditos felices cerca de la casa
                drawPig(ctx, houseX - 40, h - 50);
            } else {
                // Texto de aviso en canvas si no hay casa
                ctx.fillStyle = '#475569';
                ctx.font = 'bold 14px sans-serif';
                ctx.textAlign = 'center';
                ctx.fillText('¡Añade materiales para levantar la casa!', w / 2, h / 2);
            }

            // Dibujar Lobo si la simulación está activa
            if (wolfBlowing) {
                drawWolf(ctx, 60, h - 60);
                drawBlowParticles(ctx, 120, h - 90, houseX, houseY + 40, wolfAnimProgress);
            }
        }

        // Dibujo con primitivas del Cerdito
        function drawPig(ctx, x, y) {
            ctx.fillStyle = '#FCA5A5';
            ctx.beginPath(); ctx.arc(x, y - 20, 16, 0, Math.PI*2); ctx.fill(); // Cabeza
            ctx.beginPath(); ctx.arc(x - 6, y - 24, 3, 0, Math.PI*2); ctx.fillStyle = '#1E293B'; ctx.fill(); // Ojo
            ctx.fillStyle = '#F43F5E';
            ctx.beginPath(); ctx.arc(x, y - 18, 6, 0, Math.PI*2); ctx.fill(); // Hocico
        }

        // Dibujo con primitivas del Lobo
        function drawWolf(ctx, x, y) {
            ctx.fillStyle = '#475569';
            ctx.beginPath(); ctx.arc(x, y - 30, 22, 0, Math.PI*2); ctx.fill(); // Cabeza
            // Orejas
            ctx.beginPath(); ctx.moveTo(x - 15, y - 45); ctx.lineTo(x - 5, y - 60); ctx.lineTo(x, y - 45); ctx.fill();
            // Ojo
            ctx.fillStyle = '#EAB308';
            ctx.beginPath(); ctx.arc(x + 8, y - 34, 4, 0, Math.PI*2); ctx.fill();
            // Soplador / Hocico alargado
            ctx.fillStyle = '#334155';
            ctx.beginPath(); ctx.ellipse(x + 20, y - 25, 12, 6, 0, 0, Math.PI*2); ctx.fill();
        }

        // Partículas del soplo de viento
        function drawBlowParticles(ctx, startX, startY, targetX, targetY, progress) {
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.8)';
            ctx.lineWidth = 4;
            ctx.lineCap = 'round';

            for (let i = 0; i < 5; i++) {
                const p = (progress + i * 0.2) % 1;
                const currX = startX + (targetX - startX) * p;
                const currY = startY + (i * 8 - 16);
                ctx.beginPath();
                ctx.moveTo(currX, currY);
                ctx.lineTo(currX + 25, currY + Math.sin(p * 10) * 5);
                ctx.stroke();
            }
        }

        function testWolfBlow() {
            const { resPts } = calculateCartMetrics();
            if (resPts === 0) {
                alert('¡Primero compra materiales para construir la casa antes de probar el soplo!');
                return;
            }

            const btn = document.getElementById('btn-wolf-blow');
            btn.disabled = true;
            wolfBlowing = true;
            wolfAnimProgress = 0;

            const interval = setInterval(() => {
                wolfAnimProgress += 0.05;
                drawHouseCanvas();

                if (wolfAnimProgress >= 1) {
                    clearInterval(interval);
                    wolfBlowing = false;
                    btn.disabled = false;
                    drawHouseCanvas();

                    // Resultado del test
                    if (resPts >= 5) {
                        alert('🎉 ¡ENHORABUENA! La casa ha resistido el soplo del Lobo Feroz. Las matemáticas y tu presupuesto protegieron a los cerditos.');
                    } else {
                        alert('💨 ¡OH NO! La casa no fue lo suficientemente resistente y el Lobo la derribó. Revisa tu presupuesto e invierte en materiales más fuertes como ladrillo o puerta de hierro.');
                    }
                }
            }, 50);
        }

        // ================= RENDERING DE GRÁFICOS CHART.JS =================
        function renderCharts() {
            const { spent, remaining } = calculateCartMetrics();

            // Chart 1: Donut de Presupuesto
            const ctxBudget = document.getElementById('chart-budget').getContext('2d');
            if (chartBudgetObj) chartBudgetObj.destroy();

            chartBudgetObj = new Chart(ctxBudget, {
                type: 'doughnut',
                data: {
                    labels: ['Monedas Gastadas', 'Monedas Restantes (Cambio)'],
                    datasets: [{
                        data: [spent, remaining > 0 ? remaining : 0],
                        backgroundColor: ['#C85A32', '#2D5A27'],
                        borderWidth: 2,
                        borderColor: '#ffffff'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'bottom' },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return ` ${context.label}: $${context.raw} monedas`;
                                }
                            }
                        }
                    }
                }
            });

            document.getElementById('chart-budget-desc').innerText = 
                `Has gastado el ${Math.min(100, Math.round((spent/100)*100))}% de tu presupuesto ($${spent} / $100 monedas).`;

            // Chart 2: Comparativa de Estrategias
            const { resPts } = calculateCartMetrics();
            const ctxComp = document.getElementById('chart-comparison').getContext('2d');
            if (chartComparisonObj) chartComparisonObj.destroy();

            chartComparisonObj = new Chart(ctxComp, {
                type: 'bar',
                data: {
                    labels: [
                        'Opción A (Solo Paja)', 
                        'Opción B (Madera+Paja)', 
                        'Opción C (2 Ladrillos)', 
                        'Tu Selección Actual'
                    ],
                    datasets: [
                        {
                            label: 'Costo ($ Monedas)',
                            data: [30, 60, 80, spent],
                            backgroundColor: '#8B5A2B'
                        },
                        {
                            label: 'Nivel de Resistencia (Pts)',
                            data: [3, 5, 6, resPts],
                            backgroundColor: '#2D5A27'
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { beginAtZero: true }
                    },
                    plugins: {
                        legend: { position: 'bottom' }
                    }
                }
            });
        }

        // ================= TARJETAS DE REPASO (FLASHCARDS) =================
        function updateFlashcardView() {
            const card = filteredFC[currentFCIndex];
            if (!card) return;

            document.getElementById('fc-front-text').innerText = card.front;
            document.getElementById('fc-back-text').innerText = card.back;
            document.getElementById('fc-hint-text').innerText = `💡 Pista: ${card.hint}`;
            document.getElementById('fc-current-index').innerText = currentFCIndex + 1;
            document.getElementById('fc-total-count').innerText = filteredFC.length;

            // Resetear estado de giro
            isFlipped = false;
            document.getElementById('flashcard-inner').classList.remove('rotate-y-180');
        }

        function flipFlashcard() {
            isFlipped = !isFlipped;
            const inner = document.getElementById('flashcard-inner');
            if (isFlipped) {
                inner.classList.add('rotate-y-180');
            } else {
                inner.classList.remove('rotate-y-180');
            }
        }

        function nextFlashcard() {
            if (currentFCIndex < filteredFC.length - 1) {
                currentFCIndex++;
                updateFlashcardView();
            }
        }

        function prevFlashcard() {
            if (currentFCIndex > 0) {
                currentFCIndex--;
                updateFlashcardView();
            }
        }

        function filterFlashcards() {
            const cat = document.getElementById('flashcard-filter').value;
            if (cat === 'ALL') {
                filteredFC = [...flashcardsData];
            } else {
                filteredFC = flashcardsData.filter(c => c.cat === cat);
            }
            currentFCIndex = 0;
            updateFlashcardView();
        }

        // ================= HOJA DEL ARQUITECTO (WORKSHEET) =================
        function updateWorksheetView() {
            ['paja', 'madera', 'ladrillos', 'puerta'].forEach(i => {
                const qty = state.cart[i];
                const price = state.prices[i];
                document.getElementById(`ws-qty-${i}`).innerText = qty;
                document.getElementById(`ws-op-${i}`).innerText = `${price} × ${qty}`;
            });
        }

        function validateWorksheet() {
            const valPaja = parseInt(document.getElementById('ws-val-paja').value) || 0;
            const valMadera = parseInt(document.getElementById('ws-val-madera').value) || 0;
            const valLadrillos = parseInt(document.getElementById('ws-val-ladrillos').value) || 0;
            const valPuerta = parseInt(document.getElementById('ws-val-puerta').value) || 0;
            const userTotal = parseInt(document.getElementById('ws-user-total').value) || 0;
            const userChange = parseInt(document.getElementById('ws-user-change').value) || 0;

            const realPaja = state.cart.paja * state.prices.paja;
            const realMadera = state.cart.madera * state.prices.madera;
            const realLadrillos = state.cart.ladrillos * state.prices.ladrillos;
            const realPuerta = state.cart.puerta * state.prices.puerta;

            const realTotal = realPaja + realMadera + realLadrillos + realPuerta;
            const realChange = 100 - realTotal;

            const fb = document.getElementById('ws-feedback');
            fb.classList.remove('hidden');

            const isMultCorrect = (valPaja === realPaja) && (valMadera === realMadera) && 
                                  (valLadrillos === realLadrillos) && (valPuerta === realPuerta);
            const isTotalCorrect = (userTotal === realTotal);
            const isChangeCorrect = (userChange === realChange);

            if (isMultCorrect && isTotalCorrect && isChangeCorrect) {
                fb.className = 'px-4 py-2.5 bg-green-100 text-green-800 rounded-lg text-xs font-bold border border-green-300';
                fb.innerText = '✅ ¡Aprobado por el Tiendero Castor! Todos tus cálculos matemáticos son 100% correctos.';
            } else {
                fb.className = 'px-4 py-2.5 bg-red-100 text-red-800 rounded-lg text-xs font-bold border border-red-300';
                fb.innerText = '❌ El Tiendero Castor encontró errores en tus operaciones. Revisa la multiplicación de materiales, la suma total o la resta del cambio.';
            }
        }

        // ================= INICIALIZACIÓN =================
        window.addEventListener('DOMContentLoaded', () => {
            updateTotals();
            updateFlashcardView();
        });
    </script>
</body>
</html>
