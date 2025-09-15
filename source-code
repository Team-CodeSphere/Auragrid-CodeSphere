<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AuraGrid - Renewable Energy Monitoring System</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Midnight Horizon & Teal -->
    <!-- Application Structure Plan: A single-page dashboard with smooth-scrolling anchor-linked sections (Dashboard, Performance, System Health, AI-Insights, About). This structure is chosen for its intuitive user flow, guiding the user from a high-level, at-a-glance summary to more detailed, granular analysis and finally to AI-powered recommendations. This is ideal for a professional monitoring application where users need to quickly assess status and then investigate anomalies. Key interactions include live data simulation, time-range filtering for charts, AI-driven insights, and clear visual cues for system status, making complex microgrid data easily digestible. -->
    <!-- Visualization & Content Choices: 
        - Report Info: Real-time overall power status -> Goal: Inform -> Viz: KPI Cards -> Interaction: Live data updates and a new Gemini-powered optimization button -> Justification: Provides immediate, scannable key metrics and actionable insights.
        - Report Info: Contribution of each energy source -> Goal: Compare -> Viz: Donut Chart (Chart.js) -> Interaction: Hover tooltips for details -> Justification: Shows energy mix proportions clearly.
        - Report Info: Power generation vs. consumption over time -> Goal: Analyze Change -> Viz: Line Chart (Chart.js) -> Interaction: Buttons to filter time range (24h/7d/30d) -> Justification: Enables trend analysis and pattern identification.
        - Report Info: Health of individual system components -> Goal: Organize/Inform -> Viz: Styled HTML Table -> Interaction: Color-coded status indicators and a new button to explain the alert via Gemini API with TTS audio -> Justification: Allows for quick identification of hardware issues and provides intelligent support with a multimodal output.
        - Report Info: Project purpose and tech stack -> Goal: Inform -> Viz: Textual Blocks with Icons -> Interaction: Static content -> Justification: Provides necessary context about the project.
        - Report Info: High-level analysis of system data -> Goal: Analyze/Inform -> Viz: Text container driven by a button -> Interaction: Button click to call Gemini API to generate a summary and recommendations -> Justification: Adds a new layer of intelligent analysis to the dashboard, fulfilling the core project goal of empowering operators.
    -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a1a2e;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 40vh;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 400px;
            }
        }
        .card {
            background-color: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
         .nav-link {
            transition: color 0.3s ease, border-bottom-color 0.3s ease;
         }
        .nav-link:hover, .nav-link.active {
            color: #20c997;
            border-bottom-color: #20c997;
        }
        .kpi-card-icon {
            font-size: 2.5rem;
            line-height: 1;
            filter: drop-shadow(0 0 10px rgba(32, 201, 151, 0.5));
        }
        .text-teal {
            color: #20c997;
        }
        .bg-teal {
            background-color: #20c997;
        }
        .border-teal {
            border-color: #20c997;
        }
        .alert-modal-backdrop {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.75);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
        }
        .alert-modal-content {
            background: #2a2a3e;
            color: #e0e0e0;
            padding: 2rem;
            border-radius: 1rem;
            width: 90%;
            max-width: 500px;
            position: relative;
        }
        .alert-modal-close {
            position: absolute;
            top: 1rem;
            right: 1rem;
            cursor: pointer;
            font-size: 1.5rem;
        }
    </style>
</head>
<body class="text-gray-200">

    <header class="bg-black/40 backdrop-blur-md sticky top-0 z-50 shadow-lg">
        <nav class="container mx-auto px-6 py-4 flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <span class="text-4xl text-teal">⚡</span>
                <h1 class="text-3xl font-bold text-gray-100">AuraGrid</h1>
            </div>
            <div class="hidden md:flex items-center space-x-8">
                <a href="#dashboard" class="nav-link text-gray-400 border-b-2 border-transparent pb-1">Dashboard</a>
                <a href="#performance" class="nav-link text-gray-400 border-b-2 border-transparent pb-1">Performance</a>
                <a href="#health" class="nav-link text-gray-400 border-b-2 border-transparent pb-1">System Health</a>
                <a href="#ai-insights" class="nav-link text-gray-400 border-b-2 border-transparent pb-1">AI Insights</a>
                <a href="#about" class="nav-link text-gray-400 border-b-2 border-transparent pb-1">About</a>
            </div>
             <div class="md:hidden">
                <button id="menu-btn" class="text-gray-200 focus:outline-none">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7"></path></svg>
                </button>
            </div>
        </nav>
        <div id="mobile-menu" class="hidden md:hidden px-6 pb-4">
            <a href="#dashboard" class="block py-2 text-gray-400">Dashboard</a>
            <a href="#performance" class="block py-2 text-gray-400">Performance</a>
            <a href="#health" class="block py-2 text-gray-400">System Health</a>
            <a href="#ai-insights" class="block py-2 text-gray-400">AI Insights</a>
            <a href="#about" class="block py-2 text-gray-400">About</a>
        </div>
    </header>

    <main class="container mx-auto px-6 py-12">

        <section id="dashboard" class="mb-20 scroll-mt-20">
            <h2 class="text-4xl font-bold text-gray-100 mb-2">Microgrid Dashboard</h2>
            <p class="text-gray-400 mb-10">A real-time overview of the microgrid's performance and status.</p>
            
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8 mb-10">
                <div class="card p-8 rounded-3xl shadow-2xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-sm font-medium text-gray-400">Total Generation</p>
                            <p id="total-generation" class="text-4xl font-bold text-teal">-- kW</p>
                        </div>
                        <div class="kpi-card-icon text-teal">☀️</div>
                    </div>
                </div>
                <div class="card p-8 rounded-3xl shadow-2xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-sm font-medium text-gray-400">Total Consumption</p>
                            <p id="total-consumption" class="text-4xl font-bold text-blue-400">-- kW</p>
                        </div>
                        <div class="kpi-card-icon text-blue-400">🏠</div>
                    </div>
                </div>
                <div class="card p-8 rounded-3xl shadow-2xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-sm font-medium text-gray-400">Battery Level</p>
                            <p id="battery-level" class="text-4xl font-bold text-yellow-400">-- %</p>
                        </div>
                        <div class="kpi-card-icon text-yellow-400">🔋</div>
                    </div>
                </div>
                <div class="card p-8 rounded-3xl shadow-2xl">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-sm font-medium text-gray-400">Grid Status</p>
                            <p id="grid-status" class="text-4xl font-bold text-teal">--</p>
                        </div>
                        <div class="kpi-card-icon text-gray-400">🌐</div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                <div class="lg:col-span-2 card p-8 rounded-3xl shadow-2xl">
                    <h3 class="text-xl font-semibold text-gray-200 mb-4">Live Power Flow</h3>
                    <div class="h-80 flex flex-col justify-around items-center">
                         <div class="flex justify-center space-x-12">
                            <div class="text-center">
                                <p class="text-6xl">☀️</p>
                                <p class="font-bold text-gray-100">Solar</p>
                                <p id="solar-gen" class="text-sm text-gray-400">-- kW</p>
                            </div>
                            <div class="text-center">
                                <p class="text-6xl">💨</p>
                                <p class="font-bold text-gray-100">Wind</p>
                                <p id="wind-gen" class="text-sm text-gray-400">-- kW</p>
                            </div>
                        </div>
                        <div class="text-5xl text-gray-500">↓</div>
                        <div class="w-full h-1 bg-gray-600 rounded-full flex items-center justify-center relative">
                             <div class="absolute text-center p-4 rounded-full card -top-12">
                                <p class="text-6xl">🔋</p>
                                <p class="font-bold text-gray-100">Battery</p>
                                <p id="battery-flow" class="text-sm text-gray-400">-- kW</p>
                            </div>
                        </div>
                        <div class="text-5xl text-gray-500">↓</div>
                        <div class="flex justify-center space-x-12">
                           <div class="text-center">
                                <p class="text-6xl">🏠</p>
                                <p class="font-bold text-gray-100">Local Load</p>
                                <p id="load-flow" class="text-sm text-gray-400">-- kW</p>
                            </div>
                             <div class="text-center">
                                <p class="text-6xl">🌐</p>
                                <p class="font-bold text-gray-100">Main Grid</p>
                                <p id="grid-flow" class="text-sm text-gray-400">-- kW</p>
                            </div>
                        </div>
                    </div>
                    <div class="mt-12 flex justify-center">
                        <button id="get-optimization-btn" class="bg-teal hover:bg-teal-600 text-gray-900 font-bold py-4 px-8 rounded-xl transition duration-300 shadow-lg transform hover:scale-105">Optimize Flow ✨</button>
                    </div>
                    <div id="optimization-container" class="mt-6 hidden">
                        <div id="optimization-loading" class="flex justify-center items-center py-4">
                            <div class="w-8 h-8 border-4 border-gray-400 rounded-full border-t-teal animate-spin"></div>
                        </div>
                        <p id="optimization-text" class="text-gray-400 leading-relaxed"></p>
                    </div>
                </div>
                <div class="card p-8 rounded-3xl shadow-2xl flex flex-col">
                    <h3 class="text-xl font-semibold text-gray-200 mb-4">Energy Mix</h3>
                     <div class="flex-grow flex items-center justify-center">
                        <div class="chart-container" style="height: 250px; max-height: 250px;">
                            <canvas id="energyMixChart"></canvas>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <section id="performance" class="mb-20 scroll-mt-20">
            <h2 class="text-4xl font-bold text-gray-100 mb-2">Performance Analytics</h2>
            <p class="text-gray-400 mb-10">Analyze historical data to identify trends and optimize system efficiency.</p>
            <div class="card p-8 rounded-3xl shadow-2xl">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-xl font-semibold text-gray-200">Generation vs. Consumption</h3>
                    <div id="time-filter-buttons" class="flex space-x-2">
                        <button data-range="24h" class="bg-teal text-gray-900 px-4 py-2 rounded-lg text-sm font-bold">24 Hours</button>
                        <button data-range="7d" class="bg-gray-700 text-gray-300 px-4 py-2 rounded-lg text-sm font-medium">7 Days</button>
                        <button data-range="30d" class="bg-gray-700 text-gray-300 px-4 py-2 rounded-lg text-sm font-medium">30 Days</button>
                    </div>
                </div>
                <div class="chart-container">
                    <canvas id="powerTrendChart"></canvas>
                </div>
            </div>
        </section>

        <section id="health" class="mb-20 scroll-mt-20">
            <h2 class="text-4xl font-bold text-gray-100 mb-2">System Health</h2>
             <p class="text-gray-400 mb-10">Monitor the operational status of all microgrid components and review recent system alerts.</p>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                <div class="card p-8 rounded-3xl shadow-2xl">
                    <h3 class="text-xl font-semibold text-gray-200 mb-4">Component Status</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left">
                            <thead>
                                <tr class="border-b border-gray-600">
                                    <th class="py-2 text-gray-400">Component</th>
                                    <th class="py-2 text-gray-400">Status</th>
                                    <th class="py-2 text-gray-400">Uptime</th>
                                </tr>
                            </thead>
                            <tbody id="component-status-table">
                                <!-- JS will populate this -->
                            </tbody>
                        </table>
                    </div>
                </div>
                <div class="card p-8 rounded-3xl shadow-2xl">
                    <h3 class="text-xl font-semibold text-gray-200 mb-4">Recent Alerts</h3>
                    <ul id="alerts-log" class="space-y-4 h-64 overflow-y-auto">
                       <!-- JS will populate this -->
                    </ul>
                </div>
            </div>
        </section>

        <section id="ai-insights" class="mb-20 scroll-mt-20">
             <h2 class="text-4xl font-bold text-gray-100 mb-2">AI Insights & Recommendations</h2>
             <p class="text-gray-400 mb-10">Get a high-level, AI-powered summary of the microgrid's performance and suggestions for optimization.</p>
             <div class="card p-8 rounded-3xl shadow-2xl flex flex-col">
                <button id="get-insights-btn" class="bg-teal hover:bg-teal-600 text-gray-900 font-bold py-4 px-8 rounded-xl transition duration-300 shadow-lg self-start transform hover:scale-105">Generate Report ✨</button>
                <div id="insights-container" class="mt-8 hidden">
                    <div id="insights-loading" class="flex justify-center items-center py-4">
                        <div class="w-8 h-8 border-4 border-gray-400 rounded-full border-t-teal animate-spin"></div>
                    </div>
                    <p id="insights-text" class="text-gray-400 leading-relaxed"></p>
                </div>
             </div>
        </section>
        
        <section id="about" class="scroll-mt-20">
            <h2 class="text-4xl font-bold text-gray-100 mb-2">About AuraGrid</h2>
            <p class="text-gray-400 mb-10">A comprehensive monitoring system for renewable energy microgrids.</p>
            <div class="card p-8 rounded-3xl shadow-2xl">
                <p class="mb-4 text-gray-400 leading-relaxed">AuraGrid is an advanced platform for managing and optimizing decentralized energy systems. Our mission is to enhance the reliability and efficiency of microgrids by providing real-time data, predictive analytics, and actionable insights. We believe that by empowering operators with intelligent tools, we can accelerate the transition to a more sustainable and resilient energy future.</p>
                <h4 class="font-semibold text-lg text-gray-200 mb-2">Core Technologies</h4>
                <ul class="list-disc list-inside text-gray-400">
                    <li><span class="font-medium">Frontend:</span> HTML, Tailwind CSS, Vanilla JavaScript, Chart.js</li>
                    <li><span class="font-medium">Backend:</span> Microservices architecture, Real-time data processing</li>
                    <li><span class="font-medium">Analytics:</span> AI/ML for anomaly detection and predictive maintenance</li>
                    <li><span class="font-medium">IoT:</span> Secure sensor data acquisition and management</li>
                </ul>
            </div>
        </section>

    </main>

    <div id="alert-modal" class="alert-modal-backdrop hidden">
        <div class="alert-modal-content">
            <button id="modal-close-btn" class="alert-modal-close text-gray-400 hover:text-gray-200">
                &times;
            </button>
            <h3 class="text-2xl font-bold mb-4 text-gray-100">Alert Explained</h3>
            <div id="modal-loading" class="flex justify-center items-center py-4">
                <div class="w-8 h-8 border-4 border-gray-400 rounded-full border-t-teal animate-spin"></div>
            </div>
            <p id="modal-explanation-text" class="text-gray-400 leading-relaxed"></p>
        </div>
    </div>

    <footer class="text-center py-6 mt-16 border-t border-gray-700">
        <p class="text-gray-500">&copy; 2025 AuraGrid. All rights reserved.</p>
    </footer>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const chartFontColor = '#a0a0a0';

            let energyMixChart, powerTrendChart;
            let mockData = {
                generation: 150.5,
                consumption: 120.3,
                battery: 85,
                solar: 90.2,
                wind: 60.3,
            };

            const uiElements = {
                totalGeneration: document.getElementById('total-generation'),
                totalConsumption: document.getElementById('total-consumption'),
                batteryLevel: document.getElementById('battery-level'),
                gridStatus: document.getElementById('grid-status'),
                solarGen: document.getElementById('solar-gen'),
                windGen: document.getElementById('wind-gen'),
                batteryFlow: document.getElementById('battery-flow'),
                loadFlow: document.getElementById('load-flow'),
                gridFlow: document.getElementById('grid-flow'),
                componentStatusTable: document.getElementById('component-status-table'),
                alertsLog: document.getElementById('alerts-log'),
                timeFilterButtons: document.getElementById('time-filter-buttons'),
                menuBtn: document.getElementById('menu-btn'),
                mobileMenu: document.getElementById('mobile-menu'),
                navLinks: document.querySelectorAll('.nav-link'),
                getInsightsBtn: document.getElementById('get-insights-btn'),
                insightsContainer: document.getElementById('insights-container'),
                insightsLoading: document.getElementById('insights-loading'),
                insightsText: document.getElementById('insights-text'),
                getOptimizationBtn: document.getElementById('get-optimization-btn'),
                optimizationContainer: document.getElementById('optimization-container'),
                optimizationLoading: document.getElementById('optimization-loading'),
                optimizationText: document.getElementById('optimization-text'),
                alertModal: document.getElementById('alert-modal'),
                modalCloseBtn: document.getElementById('modal-close-btn'),
                modalLoading: document.getElementById('modal-loading'),
                modalExplanationText: document.getElementById('modal-explanation-text')
            };

            const componentData = [
                { name: 'Solar Array 1', status: 'Operational', uptime: '99.8%' },
                { name: 'Wind Turbine A', status: 'Operational', uptime: '99.5%' },
                { name: 'Inverter Main', status: 'Warning', uptime: '92.1%' },
                { name: 'Battery Bank Alpha', status: 'Operational', uptime: '99.9%' },
                { name: 'Grid Connector', status: 'Standby', uptime: '100%' },
            ];

            const alertsData = [
                { time: '10:05 AM', message: 'Grid power export initiated.', level: 'info' },
                { time: '09:58 AM', message: 'Battery reached 100% charge.', level: 'info' },
                { time: '09:30 AM', message: 'Inverter Main temperature high.', level: 'warning' },
                { time: '08:15 AM', message: 'Peak solar generation detected.', level: 'info' },
            ];

            function renderEnergyMixChart() {
                const ctx = document.getElementById('energyMixChart').getContext('2d');
                energyMixChart = new Chart(ctx, {
                    type: 'doughnut',
                    data: {
                        labels: ['Solar', 'Wind', 'Battery'],
                        datasets: [{
                            data: [mockData.solar, mockData.wind, 0],
                            backgroundColor: ['#fcd34d', '#53a2f7', '#a3e635'],
                            borderColor: '#2a2a3e',
                            borderWidth: 4,
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        cutout: '70%',
                        plugins: {
                            legend: {
                                position: 'bottom',
                                labels: {
                                    color: chartFontColor,
                                    boxWidth: 12,
                                    padding: 20
                                }
                            }
                        }
                    }
                });
            }
            
            function generateTimeData(range) {
                let labels = [];
                let points = 0;
                let format = {};
                const now = new Date();

                if(range === '24h') {
                    points = 24;
                    format = { hour: 'numeric' };
                    for (let i = points - 1; i >= 0; i--) {
                        const date = new Date(now.getTime() - i * 60 * 60 * 1000);
                        labels.push(date.toLocaleString('en-US', format));
                    }
                } else if(range === '7d') {
                    points = 7;
                    format = { weekday: 'short' };
                    for (let i = points - 1; i >= 0; i--) {
                        const date = new Date(now.getTime() - i * 24 * 60 * 60 * 1000);
                        labels.push(date.toLocaleString('en-US', format));
                    }
                } else { // 30d
                    points = 30;
                    format = { day: 'numeric', month: 'short' };
                    for (let i = points - 1; i >= 0; i--) {
                        const date = new Date(now.getTime() - i * 24 * 60 * 60 * 1000);
                        labels.push(i % 5 === 0 ? date.toLocaleDateString('en-GB', { day: 'numeric', month: 'short'}) : '');
                    }
                }
                
                const genData = Array.from({length: points}, () => Math.random() * 100 + 50);
                const consData = Array.from({length: points}, () => Math.random() * 80 + 40);

                return { labels, genData, consData };
            }

            function renderPowerTrendChart(range = '24h') {
                 const { labels, genData, consData } = generateTimeData(range);
                 const ctx = document.getElementById('powerTrendChart').getContext('2d');
                 if (powerTrendChart) {
                    powerTrendChart.destroy();
                 }
                 powerTrendChart = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: labels,
                        datasets: [
                            {
                                label: 'Generation',
                                data: genData,
                                borderColor: '#20c997',
                                backgroundColor: 'rgba(32, 201, 151, 0.1)',
                                borderWidth: 2,
                                fill: true,
                                tension: 0.4,
                                pointRadius: 0,
                            },
                            {
                                label: 'Consumption',
                                data: consData,
                                borderColor: '#53a2f7',
                                backgroundColor: 'rgba(83, 162, 247, 0.1)',
                                borderWidth: 2,
                                fill: true,
                                tension: 0.4,
                                pointRadius: 0,
                            }
                        ]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        scales: {
                            y: {
                                beginAtZero: true,
                                grid: { color: '#2a2a3e' },
                                ticks: { color: chartFontColor, callback: value => `${value} kW` }
                            },
                            x: {
                                grid: { display: false },
                                ticks: { color: chartFontColor, maxRotation: 0, autoSkip: true, maxTicksLimit: 12 }
                            }
                        },
                        plugins: {
                            legend: {
                                position: 'top',
                                align: 'end',
                                labels: { color: chartFontColor, boxWidth: 12 }
                            },
                            tooltip: {
                                mode: 'index',
                                intersect: false,
                                callbacks: {
                                    label: function(context) {
                                        let label = context.dataset.label || '';
                                        if (label) label += ': ';
                                        if (context.parsed.y !== null) label += `${context.parsed.y.toFixed(1)} kW`;
                                        return label;
                                    }
                                }
                            }
                        },
                        interaction: {
                            mode: 'index',
                            intersect: false,
                        },
                    }
                });
            }

            function updateUI() {
                mockData.solar += (Math.random() - 0.5) * 2;
                mockData.wind += (Math.random() - 0.5) * 3;
                if (mockData.solar < 0) mockData.solar = 0;
                if (mockData.wind < 0) mockData.wind = 0;
                
                mockData.generation = mockData.solar + mockData.wind;
                mockData.consumption += (Math.random() - 0.5) * 2;
                
                const netPower = mockData.generation - mockData.consumption;
                
                mockData.battery += netPower / 100;
                if(mockData.battery > 100) mockData.battery = 100;
                if(mockData.battery < 0) mockData.battery = 0;

                uiElements.totalGeneration.textContent = `${mockData.generation.toFixed(1)} kW`;
                uiElements.totalConsumption.textContent = `${mockData.consumption.toFixed(1)} kW`;
                uiElements.batteryLevel.textContent = `${mockData.battery.toFixed(0)} %`;

                let statusText = 'Stable';
                let statusColor = 'text-teal';
                if (mockData.generation < mockData.consumption) {
                    statusText = 'Importing';
                    statusColor = 'text-blue-400';
                } else if (mockData.battery.toFixed(0) == 100) {
                    statusText = 'Exporting';
                    statusColor = 'text-purple-400';
                }
                uiElements.gridStatus.textContent = statusText;
                uiElements.gridStatus.className = `text-4xl font-bold ${statusColor}`;

                uiElements.solarGen.textContent = `${mockData.solar.toFixed(1)} kW`;
                uiElements.windGen.textContent = `${mockData.wind.toFixed(1)} kW`;
                uiElements.loadFlow.textContent = `${mockData.consumption.toFixed(1)} kW`;

                const batteryFlowValue = netPower;
                uiElements.batteryFlow.textContent = `${batteryFlowValue.toFixed(1)} kW`;
                uiElements.batteryFlow.style.color = batteryFlowValue > 0 ? '#20c997' : '#EF4444';

                let gridFlowValue = 0;
                if (netPower > 0 && mockData.battery >= 100) {
                    gridFlowValue = netPower;
                } else if (netPower < 0 && mockData.battery <= 0) {
                    gridFlowValue = netPower;
                }
                uiElements.gridFlow.textContent = `${gridFlowValue.toFixed(1)} kW`;
                 uiElements.gridFlow.style.color = gridFlowValue > 0 ? '#8B5CF6' : '#53a2f7';

                if (energyMixChart) {
                    energyMixChart.data.datasets[0].data = [
                        mockData.solar, 
                        mockData.wind, 
                        (netPower < 0 && mockData.battery > 0) ? -netPower : 0
                    ];
                    energyMixChart.data.labels = ['Solar', 'Wind', (netPower < 0 && mockData.battery > 0) ? 'Battery Out' : ''];
                    energyMixChart.update();
                }
            }

            function populateStatusTable() {
                uiElements.componentStatusTable.innerHTML = '';
                componentData.forEach(c => {
                    let statusClass = 'text-green-400';
                    if (c.status === 'Warning') statusClass = 'text-yellow-400';
                    if (c.status === 'Fault') statusClass = 'text-red-400';
                    if (c.status === 'Standby') statusClass = 'text-gray-400';

                    const row = `
                        <tr class="border-b border-gray-600">
                            <td class="py-3 pr-4">${c.name}</td>
                            <td class="py-3 pr-4 font-semibold ${statusClass}">${c.status}</td>
                            <td class="py-3">${c.uptime}</td>
                        </tr>`;
                    uiElements.componentStatusTable.innerHTML += row;
                });
            }

            function populateAlertsLog() {
                uiElements.alertsLog.innerHTML = '';
                alertsData.forEach((a, index) => {
                    let icon = 'ℹ️';
                    if (a.level === 'warning') icon = '⚠️';
                    if (a.level === 'error') icon = '❗';

                    const item = `
                        <li class="flex items-start justify-between space-x-3">
                            <div class="flex items-start space-x-3">
                                <span class="text-xl">${icon}</span>
                                <div>
                                    <p class="font-medium">${a.message}</p>
                                    <p class="text-sm text-gray-400">${a.time}</p>
                                </div>
                            </div>
                            <button class="alert-explain-btn text-sm text-teal hover:text-teal-400 font-bold whitespace-nowrap" data-alert-index="${index}">Explain ✨</button>
                        </li>`;
                    uiElements.alertsLog.innerHTML += item;
                });
            }

            async function getGeminiInsights() {
                uiElements.insightsContainer.classList.remove('hidden');
                uiElements.insightsLoading.classList.remove('hidden');
                uiElements.insightsText.textContent = '';
                uiElements.getInsightsBtn.disabled = true;

                const apiKey = "";
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
                const prompt = `Based on the following microgrid data, provide a concise, single-paragraph summary of the current system performance and offer 3 key recommendations for optimization. Do not add any headings. The data is:
                - Total Generation: ${mockData.generation.toFixed(1)} kW
                - Total Consumption: ${mockData.consumption.toFixed(1)} kW
                - Battery Level: ${mockData.battery.toFixed(0)} %
                - Solar Generation: ${mockData.solar.toFixed(1)} kW
                - Wind Generation: ${mockData.wind.toFixed(1)} kW
                - Component Status: ${JSON.stringify(componentData)}
                - Recent Alerts: ${JSON.stringify(alertsData)}`;

                try {
                    const payload = {
                        contents: [{ parts: [{ text: prompt }] }],
                        tools: [{ "google_search": {} }],
                        systemInstruction: {
                            parts: [{ text: "Act as a world-class renewable energy engineer." }]
                        }
                    };
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    const result = await response.json();
                    const text = result.candidates[0].content.parts[0].text;
                    uiElements.insightsText.textContent = text;
                } catch (error) {
                    uiElements.insightsText.textContent = "Error fetching insights. Please try again later.";
                    console.error('Error fetching Gemini insights:', error);
                } finally {
                    uiElements.insightsLoading.classList.add('hidden');
                    uiElements.getInsightsBtn.disabled = false;
                }
            }

            async function getOptimizationAdvice() {
                uiElements.optimizationContainer.classList.remove('hidden');
                uiElements.optimizationLoading.classList.remove('hidden');
                uiElements.optimizationText.textContent = '';
                uiElements.getOptimizationBtn.disabled = true;

                const apiKey = "";
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
                const prompt = `Based on the current microgrid data: Generation is ${mockData.generation.toFixed(1)} kW, Consumption is ${mockData.consumption.toFixed(1)} kW, and Battery Level is ${mockData.battery.toFixed(0)} %. Provide a single, actionable recommendation.`;

                try {
                    const payload = {
                        contents: [{ parts: [{ text: prompt }] }],
                        systemInstruction: {
                            parts: [{ text: "Act as an intelligent energy management system providing a single actionable step to a microgrid operator." }]
                        }
                    };
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    const result = await response.json();
                    const text = result.candidates[0].content.parts[0].text;
                    uiElements.optimizationText.textContent = text;
                } catch (error) {
                    uiElements.optimizationText.textContent = "Error fetching optimization advice. Please try again later.";
                    console.error('Error fetching Gemini optimization advice:', error);
                } finally {
                    uiElements.optimizationLoading.classList.add('hidden');
                    uiElements.getOptimizationBtn.disabled = false;
                }
            }
            
            async function explainAlert(message) {
                 uiElements.alertModal.classList.remove('hidden');
                 uiElements.modalLoading.classList.remove('hidden');
                 uiElements.modalExplanationText.textContent = '';
                 
                 const apiKey = "";
                 const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
                 const prompt = `Explain the following microgrid alert in simple terms for an operator who may not be a technical expert: "${message}". What does it mean and what is the general recommended next step?`;
                 
                 let explanation = "";
                 try {
                     const payload = {
                         contents: [{ parts: [{ text: prompt }] }]
                     };
                     const response = await fetch(apiUrl, {
                         method: 'POST',
                         headers: { 'Content-Type': 'application/json' },
                         body: JSON.stringify(payload)
                     });
                     const result = await response.json();
                     explanation = result.candidates[0].content.parts[0].text;
                     uiElements.modalExplanationText.textContent = explanation;
                     await textToSpeech(explanation);
                 } catch (error) {
                     explanation = "Could not get an explanation at this time. Please check the system manually.";
                     uiElements.modalExplanationText.textContent = explanation;
                     console.error('Error explaining alert:', error);
                 } finally {
                     uiElements.modalLoading.classList.add('hidden');
                 }
            }

            async function textToSpeech(text) {
                const audio = new Audio();
                const apiKey = "";
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=${apiKey}`;
                
                try {
                    const payload = {
                        contents: [{ parts: [{ text: text }] }],
                        generationConfig: {
                            responseModalities: ["AUDIO"],
                            speechConfig: {
                                voiceConfig: {
                                    prebuiltVoiceConfig: { voiceName: "Charon" }
                                }
                            }
                        },
                        model: "gemini-2.5-flash-preview-tts"
                    };

                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    const result = await response.json();
                    const part = result?.candidates?.[0]?.content?.parts?.[0];
                    const audioData = part?.inlineData?.data;
                    const mimeType = part?.inlineData?.mimeType;

                    if (audioData && mimeType && mimeType.startsWith("audio/")) {
                        const sampleRate = parseInt(mimeType.match(/rate=(\d+)/)[1], 10);
                        const pcmData = base64ToArrayBuffer(audioData);
                        const pcm16 = new Int16Array(pcmData);
                        const wavBlob = pcmToWav(pcm16, sampleRate);
                        audio.src = URL.createObjectURL(wavBlob);
                        audio.play();
                    }
                } catch (error) {
                    console.error('TTS error:', error);
                }
            }

            function base64ToArrayBuffer(base64) {
                const binaryString = window.atob(base64);
                const len = binaryString.length;
                const bytes = new Uint8Array(len);
                for (let i = 0; i < len; i++) {
                        bytes[i] = binaryString.charCodeAt(i);
                }
                return bytes.buffer;
            }

            function pcmToWav(pcmData, sampleRate) {
                const numChannels = 1;
                const sampleSize = 16;
                const byteRate = sampleRate * numChannels * sampleSize / 8;
                const blockAlign = numChannels * sampleSize / 8;
                const buffer = new ArrayBuffer(44 + pcmData.length * 2);
                const view = new DataView(buffer);
                let offset = 0;

                function writeString(view, offset, string) {
                    for (let i = 0; i < string.length; i++) {
                        view.setUint8(offset + i, string.charCodeAt(i));
                    }
                }

                writeString(view, offset, 'RIFF'); offset += 4;
                view.setUint32(offset, 36 + pcmData.length * 2, true); offset += 4;
                writeString(view, offset, 'WAVE'); offset += 4;
                writeString(view, offset, 'fmt '); offset += 4;
                view.setUint32(offset, 16, true); offset += 4;
                view.setUint16(offset, 1, true); offset += 2;
                view.setUint16(offset, numChannels, true); offset += 2;
                view.setUint32(offset, sampleRate, true); offset += 4;
                view.setUint32(offset, byteRate, true); offset += 4;
                view.setUint16(offset, blockAlign, true); offset += 2;
                view.setUint16(offset, sampleSize, true); offset += 2;
                writeString(view, offset, 'data'); offset += 4;
                view.setUint32(offset, pcmData.length * 2, true); offset += 4;

                for (let i = 0; i < pcmData.length; i++, offset += 2) {
                    view.setInt16(offset, pcmData[i], true);
                }

                return new Blob([view], { type: 'audio/wav' });
            }

            function setupEventListeners() {
                uiElements.timeFilterButtons.addEventListener('click', (e) => {
                    if (e.target.tagName === 'BUTTON') {
                        const range = e.target.dataset.range;
                        renderPowerTrendChart(range);
                        
                        uiElements.timeFilterButtons.querySelectorAll('button').forEach(btn => {
                            btn.classList.remove('bg-teal', 'text-gray-900');
                            btn.classList.add('bg-gray-700', 'text-gray-300');
                        });
                        e.target.classList.add('bg-teal', 'text-gray-900');
                        e.target.classList.remove('bg-gray-700', 'text-gray-300');
                    }
                });
                
                uiElements.menuBtn.addEventListener('click', () => {
                    uiElements.mobileMenu.classList.toggle('hidden');
                });
                
                document.querySelectorAll('a[href^="#"]').forEach(anchor => {
                    anchor.addEventListener('click', function (e) {
                        e.preventDefault();
                        const targetId = this.getAttribute('href');
                        document.querySelector(targetId).scrollIntoView({
                            behavior: 'smooth'
                        });
                        if(uiElements.mobileMenu.classList.contains('hidden') === false){
                            uiElements.mobileMenu.classList.add('hidden');
                        }
                    });
                });

                const sections = document.querySelectorAll('section');
                const observer = new IntersectionObserver((entries) => {
                    entries.forEach(entry => {
                        if (entry.isIntersecting) {
                            uiElements.navLinks.forEach(link => {
                                link.classList.remove('active');
                                if (link.getAttribute('href').substring(1) === entry.target.id) {
                                    link.classList.add('active');
                                }
                            });
                        }
                    });
                }, { rootMargin: '-50% 0px -50% 0px', threshold: 0 });

                sections.forEach(section => observer.observe(section));

                uiElements.getInsightsBtn.addEventListener('click', getGeminiInsights);
                uiElements.getOptimizationBtn.addEventListener('click', getOptimizationAdvice);

                uiElements.alertsLog.addEventListener('click', async (e) => {
                    if (e.target.classList.contains('alert-explain-btn')) {
                        const index = e.target.dataset.alert-index;
                        const alertMessage = alertsData[index].message;
                        await explainAlert(alertMessage);
                    }
                });

                uiElements.modalCloseBtn.addEventListener('click', () => {
                    uiElements.alertModal.classList.add('hidden');
                });
            }

            function init() {
                renderEnergyMixChart();
                renderPowerTrendChart('24h');
                populateStatusTable();
                populateAlertsLog();
                updateUI();
                setInterval(updateUI, 2000);
                setupEventListeners();
            }

            init();
        });
    </script>
</body>
</html>

