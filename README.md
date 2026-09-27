
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulação: Modificação e Controle Climático Global</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }

        body {
            background-color: #060a12;
            color: #e0f2fe;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
            display: flex;
            height: 100vh;
            width: 100vw;
        }

        #canvas-container {
            flex: 1;
            position: relative;
            height: 100%;
        }

        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }

        /* Painel de Controle Sci-Fi */
        #ui-panel {
            width: 360px;
            background: rgba(10, 18, 32, 0.88);
            backdrop-filter: blur(14px);
            border-left: 1px solid rgba(0, 242, 254, 0.25);
            padding: 22px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            box-shadow: -8px 0 30px rgba(0, 0, 0, 0.6);
            z-index: 10;
            overflow-y: auto;
        }

        h1 {
            font-size: 1.05rem;
            letter-spacing: 1.5px;
            text-transform: uppercase;
            color: #00f2fe;
            border-bottom: 1px solid rgba(0, 242, 254, 0.3);
            padding-bottom: 8px;
        }

        .metric-card {
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 8px;
            padding: 10px 14px;
        }

        .metric-title {
            font-size: 0.72rem;
            color: #94a3b8;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            margin-bottom: 2px;
        }

        .metric-value {
            font-size: 1.35rem;
            font-weight: bold;
            font-family: 'Courier New', Courier, monospace;
            color: #ffffff;
        }

        .metric-sub {
            font-size: 0.7rem;
            color: #38ef7d;
            margin-top: 2px;
        }

        .control-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        label {
            font-size: 0.78rem;
            color: #cbd5e1;
            display: flex;
            justify-content: space-between;
        }

        input[type="range"] {
            accent-color: #00f2fe;
            cursor: pointer;
        }

        .toggle-btn {
            background: rgba(0, 242, 254, 0.1);
            border: 1px solid rgba(0, 242, 254, 0.4);
            color: #00f2fe;
            padding: 8px 12px;
            border-radius: 6px;
            font-size: 0.78rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .toggle-btn:hover {
            background: rgba(0, 242, 254, 0.25);
            box-shadow: 0 0 10px rgba(0, 242, 254, 0.4);
        }

        .toggle-btn.active {
            background: #00f2fe;
            color: #060a12;
            box-shadow: 0 0 15px rgba(0, 242, 254, 0.6);
        }

        .legend {
            display: flex;
            flex-direction: column;
            gap: 8px;
            font-size: 0.72rem;
            margin-top: 5px;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            padding-top: 10px;
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .dot {
            width: 10px;
            height: 10px;
            border-radius: 50%;
        }

        .dot-ion { background: #00f2fe; box-shadow: 0 0 8px #00f2fe; }
        .dot-storm { background: #ff2a6d; box-shadow: 0 0 8px #ff2a6d; }
        .dot-rain { background: #38ef7d; box-shadow: 0 0 8px #38ef7d; }
        .dot-aerosol { background: #ffea00; box-shadow: 0 0 8px #ffea00; }
        .dot-dac { background: #a855f7; box-shadow: 0 0 8px #a855f7; }
    </style>
</head>
<body>

    <div id="canvas-container">
        <canvas id="simCanvas"></canvas>
    </div>

    <div id="ui-panel">
        <h1>Modificação Climática</h1>

        <!-- Métricas Principais -->
        <div class="metric-card">
            <div class="metric-title">Anomalia Térmica Global</div>
            <div class="metric-value" id="tempValue">+1.45 °C</div>
            <div class="metric-sub" id="tempStatus">Estabilizando via SRM</div>
        </div>

        <div class="metric-card">
            <div class="metric-title">CO₂ Atmosférico</div>
            <div class="metric-value" id="co2Value">418 ppm</div>
            <div class="metric-sub" id="dacStatus">Redução por Torres DAC</div>
        </div>

        <div class="metric-card">
            <div class="metric-title">Eventos Extremos Evitados</div>
            <div class="metric-value" id="eventsNeutralized">0</div>
            <div class="metric-sub">Furacões & Secas Dispersadas</div>
        </div>

        <div class="metric-card">
            <div class="metric-title">Produtividade Agrícola</div>
            <div class="metric-value" id="cropYield">94.8 %</div>
            <div class="metric-sub">Irrigação Pluvial Otimizada</div>
        </div>

        <!-- Controles da Geoengenharia -->
        <button class="toggle-btn active" id="btnIonizer">Dissipação por Ionização</button>
        <button class="toggle-btn active" id="btnSeeding">Semeadura Agrícola de Nuvens</button>
        
        <div class="control-group">
            <label>Injeção de Aerossóis (SRM): <span id="aerosolVal">65%</span></label>
            <input type="range" id="aerosolSlider" min="0" max="100" value="65">
        </div>

        <div class="control-group">
            <label>Potência da Rede DAC (Captura CO₂): <span id="dacVal">70%</span></label>
            <input type="range" id="dacSlider" min="0" max="100" value="70">
        </div>

        <div class="control-group">
            <label>Frequência de Furacões</label>
            <input type="range" id="stormFreqSlider" min="1" max="10" value="4">
        </div>

        <!-- Legenda -->
        <div class="legend">
            <div class="legend-item"><div class="dot dot-ion"></div> Feixe Ionizador Desintegrador</div>
            <div class="legend-item"><div class="dot dot-storm"></div> Ciclone / Furacão de Alta Energia</div>
            <div class="legend-item"><div class="dot dot-rain"></div> Semeadura e Precipitação Controlada</div>
            <div class="legend-item"><div class="dot dot-aerosol"></div> Camada de Refletividade Sol (SRM)</div>
            <div class="legend-item"><div class="dot dot-dac"></div> Exaustão Reversa de Carbono (DAC)</div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Estado do Sistema de Controle Climático
        const state = {
            ionizerActive: true,
            cloudSeedingActive: true,
            aerosolReflectivity: 0.65,
            dacPower: 0.70,
            stormSpawnRate: 0.008,
            
            // Dados Ambientais
            globalTempAnomaly: 1.45,
            co2Level: 418.0,
            neutralizedCount: 0,
            cropYieldPct: 94.8,

            // Entidades
            storms: [],
            cloudClusters: [],
            ionStations: [],
            dacTowers: [],
            rainParticles: [],
            solarRays: [],
            co2Particles: []
        };

        // Elementos DOM
        const btnIonizer = document.getElementById('btnIonizer');
        const btnSeeding = document.getElementById('btnSeeding');
        const aerosolSlider = document.getElementById('aerosolSlider');
        const dacSlider = document.getElementById('dacSlider');
        const stormFreqSlider = document.getElementById('stormFreqSlider');

        btnIonizer.addEventListener('click', () => {
            state.ionizerActive = !state.ionizerActive;
            btnIonizer.classList.toggle('active', state.ionizerActive);
        });

        btnSeeding.addEventListener('click', () => {
            state.cloudSeedingActive = !state.cloudSeedingActive;
            btnSeeding.classList.toggle('active', state.cloudSeedingActive);
        });

        aerosolSlider.addEventListener('input', (e) => {
            state.aerosolReflectivity = parseInt(e.target.value) / 100;
            document.getElementById('aerosolVal').innerText = `${e.target.value}%`;
        });

        dacSlider.addEventListener('input', (e) => {
            state.dacPower = parseInt(e.target.value) / 100;
            document.getElementById('dacVal').innerText = `${e.target.value}%`;
        });

        stormFreqSlider.addEventListener('input', (e) => {
            state.stormSpawnRate = parseInt(e.target.value) * 0.002;
        });

        // Classe de Estação Ionizadora de Radiação / Pulso Atômico
        class IonStation {
            constructor(x, y, label) {
                this.x = x;
                this.y = y;
                this.label = label;
                this.beamTarget = null;
                this.charge = 1.0;
            }

            draw() {
                ctx.save();
                // Base da Estação
                ctx.beginPath();
                ctx.arc(this.x, this.y, 10, 0, Math.PI * 2);
                ctx.fillStyle = "#0a192f";
                ctx.strokeStyle = "#00f2fe";
                ctx.lineWidth = 2;
                ctx.fill();
                ctx.stroke();

                // Domo Ionizador
                ctx.beginPath();
                ctx.arc(this.x, this.y, 4, 0, Math.PI * 2);
                ctx.fillStyle = "#00f2fe";
                ctx.shadowColor = "#00f2fe";
                ctx.shadowBlur = 10;
                ctx.fill();

                // Rótulo
                ctx.fillStyle = "#94a3b8";
                ctx.font = "10px sans-serif";
                ctx.fillText(this.label, this.x - 22, this.y + 22);
                ctx.restore();
            }
        }

        // Classe de Torre DAC (Direct Air Capture)
        class DACTower {
            constructor(x, y) {
                this.x = x;
                this.y = y;
            }

            draw() {
                ctx.save();
                ctx.fillStyle = "#a855f7";
                ctx.shadowColor = "#a855f7";
                ctx.shadowBlur = 8;
                ctx.fillRect(this.x - 4, this.y - 12, 8, 12);
                
                // Anel de Aspiração de Carbono
                ctx.beginPath();
                ctx.arc(this.x, this.y - 12, 6, 0, Math.PI * 2);
                ctx.strokeStyle = "rgba(168, 85, 247, 0.6)";
                ctx.stroke();
                ctx.restore();
            }
        }

        // Classe de Ciclone / Furacão Extremo
        class Storm {
            constructor() {
                this.x = Math.random() * (canvas.width * 0.5) + canvas.width * 0.1;
                this.y = Math.random() * (canvas.height * 0.4) + canvas.height * 0.2;
                this.radius = 35 + Math.random() * 35;
                this.maxRadius = this.radius;
                this.intensity = 1.0; // 1.0 = Categoria 5
                this.angle = 0;
                this.vx = (Math.random() - 0.2) * 0.6;
                this.vy = (Math.random() - 0.5) * 0.4;
                this.beingNeutralized = false;
            }

            update() {
                this.x += this.vx;
                this.y += this.vy;
                this.angle += 0.04 * this.intensity;

                // Limites de Borda
                if (this.x < 50 || this.x > canvas.width - 50) this.vx *= -1;
                if (this.y < 50 || this.y > canvas.height - 150) this.vy *= -1;
            }

            draw() {
                ctx.save();
                ctx.translate(this.x, this.y);
                ctx.rotate(this.angle);

                // Espirais de Vento do Furacão
                const arms = 4;
                for (let i = 0; i < arms; i++) {
                    const armAngle = (Math.PI * 2 / arms) * i;
                    ctx.beginPath();
                    ctx.arc(0, 0, this.radius, armAngle, armAngle + 1.2);
                    ctx.lineWidth = 4 * this.intensity;
                    ctx.strokeStyle = this.beingNeutralized 
                        ? `rgba(0, 242, 254, ${0.4 * this.intensity})` 
                        : `rgba(255, 42, 109, ${0.7 * this.intensity})`;
                    ctx.shadowColor = this.beingNeutralized ? "#00f2fe" : "#ff2a6d";
                    ctx.shadowBlur = 12;
                    ctx.stroke();
                }

                // Olho do Furacão
                ctx.beginPath();
                ctx.arc(0, 0, 6 * this.intensity, 0, Math.PI * 2);
                ctx.fillStyle = "#060a12";
                ctx.fill();

                ctx.restore();
            }
        }

        // Classe de Nuvens Agrícolas e Semeadura Pluvial
        class CloudCluster {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.width = 60 + Math.random() * 40;
                this.rainActive = false;
            }

            update() {
                this.x += 0.3;
                if (this.x > canvas.width + 50) this.x = -100;
            }

            draw() {
                ctx.save();
                ctx.fillStyle = this.rainActive ? "rgba(56, 239, 125, 0.25)" : "rgba(255, 255, 255, 0.15)";
                
                // Formato de nuvem estilizado
                ctx.beginPath();
                ctx.arc(this.x, this.y, 20, 0, Math.PI * 2);
                ctx.arc(this.x + 20, this.y - 10, 25, 0, Math.PI * 2);
                ctx.arc(this.x + 45, this.y, 20, 0, Math.PI * 2);
                ctx.fill();

                if (this.rainActive) {
                    ctx.strokeStyle = "rgba(56, 239, 125, 0.5)";
                    ctx.lineWidth = 1;
                    ctx.setLineDash([2, 4]);
                    ctx.beginPath();
                    ctx.moveTo(this.x, this.y + 10);
                    ctx.lineTo(this.x, this.y + 40);
                    ctx.moveTo(this.x + 25, this.y + 10);
                    ctx.lineTo(this.x + 25, this.y + 45);
                    ctx.stroke();
                }
                ctx.restore();
            }
        }

        // Inicializar Instalações Terrestres
        function initInfrastructure() {
            state.ionStations = [
                new IonStation(canvas.width * 0.15, canvas.height * 0.75, "Ion-Alpha (Atlântico)"),
                new IonStation(canvas.width * 0.50, canvas.height * 0.80, "Ion-Beta (Equatorial)"),
                new IonStation(canvas.width * 0.82, canvas.height * 0.72, "Ion-Gamma (Pacífico)")
            ];

            state.dacTowers = [
                new DACTower(canvas.width * 0.25, canvas.height * 0.78),
                new DACTower(canvas.width * 0.38, canvas.height * 0.82),
                new DACTower(canvas.width * 0.65, canvas.height * 0.79),
                new DACTower(canvas.width * 0.75, canvas.height * 0.81)
            ];

            state.cloudClusters = [
                new CloudCluster(100, canvas.height * 0.35),
                new CloudCluster(350, canvas.height * 0.28),
                new CloudCluster(650, canvas.height * 0.38)
            ];
        }

        // Partículas CO₂ sendo capturadas
        function spawnCO2Particle() {
            if (Math.random() < state.dacPower * 0.4) {
                state.co2Particles.push({
                    x: Math.random() * canvas.width,
                    y: Math.random() * (canvas.height * 0.6),
                    targetTower: state.dacTowers[Math.floor(Math.random() * state.dacTowers.length)],
                    progress: 0
                });
            }
        }

        // Loop de Renderização e Simulação
        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // 1. Desenhar Fundo e Mapa Biomático Simulado
            drawMapBackground();

            // 2. Desenhar Camada de Aerossóis Estratosféricos (SRM - Solar Radiation Management)
            drawAerosolLayer();

            // 3. Raio Solares e Refletividade do Albedo
            updateAndDrawSolarRays();

            // 4. Desenhar Torres DAC e Animação de Absorção de CO₂
            drawDACTowersAndParticles();

            // 5. Atualizar e Desenhar Nuvens e Semeadura Agrícola
            updateAndDrawClouds();

            // 6. Gerenciar Ciclones / Furacões
            manageStorms();

            // 7. Atualizar e Desenhar Estações Ionizadoras
            updateAndDrawIonizers();

            // 8. Atualizar Métricas Dinâmicas
            updateEnvironmentMetrics();

            requestAnimationFrame(animate);
        }

        // Renderização do Mapa Terrestre em Estilo "Grid Sci-Fi"
        function drawMapBackground() {
            const h = canvas.height;
            const w = canvas.width;

            // Continentes/Zonas Agrícolas Iluminadas
            ctx.save();
            ctx.fillStyle = "rgba(16, 30, 50, 0.6)";
            
            // Região Agrícola A (América / Ásia Fictícia)
            ctx.beginPath();
            ctx.roundRect(w * 0.1, h * 0.45, w * 0.35, h * 0.35, 20);
            ctx.roundRect(w * 0.55, h * 0.48, w * 0.38, h * 0.32, 20);
            ctx.fill();

            // Destaque Verde para Zonas Agrícolas Féis
            ctx.fillStyle = "rgba(56, 239, 125, 0.08)";
            ctx.fillRect(w * 0.12, h * 0.5, w * 0.3, h * 0.22);
            ctx.fillRect(w * 0.58, h * 0.52, w * 0.32, h * 0.22);

            // Rótulos
            ctx.fillStyle = "rgba(56, 239, 125, 0.6)";
            ctx.font = "11px sans-serif";
            ctx.fillText("ZONA AGRÍCOLA OTIMIZADA - NORTE", w * 0.14, h * 0.53);
            ctx.fillText("MATRIZ DE CULTIVO GLOBAL - SUL", w * 0.60, h * 0.55);

            ctx.restore();
        }

        // Camada de Refletividade Sol (Injeção de Aerossóis)
        function drawAerosolLayer() {
            if (state.aerosolReflectivity <= 0) return;

            ctx.save();
            const yStratosphere = canvas.height * 0.12;
            const grad = ctx.createLinearGradient(0, yStratosphere - 15, 0, yStratosphere + 15);
            grad.addColorStop(0, "rgba(255, 234, 0, 0)");
            grad.addColorStop(0.5, `rgba(255, 234, 0, ${state.aerosolReflectivity * 0.35})`);
            grad.addColorStop(1, "rgba(255, 234, 0, 0)");

            ctx.fillStyle = grad;
            ctx.fillRect(0, yStratosphere - 15, canvas.width, 30);

            // Partículas cintilantes de aerossol
            ctx.fillStyle = `rgba(255, 234, 0, ${state.aerosolReflectivity * 0.7})`;
            for (let i = 0; i < 30; i++) {
                const px = (Math.sin(i * 99 + Date.now() * 0.001) * 0.5 + 0.5) * canvas.width;
                const py = yStratosphere + Math.cos(i * 33) * 8;
                ctx.fillRect(px, py, 2, 2);
            }

            ctx.fillStyle = "rgba(255, 234, 0, 0.7)";
            ctx.font = "10px sans-serif";
            ctx.fillText(`CAMADA ESTRATOSFÉRICA SRM (ALBEDO: ${(state.aerosolReflectivity * 100).toFixed(0)}%)`, 20, yStratosphere - 8);
            ctx.restore();
        }

        // Animação de Raios Solares Sendo Refletidos
        function updateAndDrawSolarRays() {
            ctx.save();
            const rayCount = 8;
            const yStrato = canvas.height * 0.12;

            for (let i = 0; i < rayCount; i++) {
                const rx = (canvas.width / rayCount) * i + 40;
                
                // Raio vindo do espaço
                ctx.beginPath();
                ctx.moveTo(rx - 30, 0);
                ctx.lineTo(rx, yStrato);
                ctx.strokeStyle = "rgba(255, 234, 0, 0.25)";
                ctx.lineWidth = 2;
                ctx.stroke();

                // Raio Refletido de volta ao espaço
                if (state.aerosolReflectivity > 0.1) {
                    ctx.beginPath();
                    ctx.moveTo(rx, yStrato);
                    ctx.lineTo(rx + 25 * state.aerosolReflectivity, 0);
                    ctx.strokeStyle = `rgba(0, 242, 254, ${state.aerosolReflectivity * 0.5})`;
                    ctx.lineWidth = 1.5;
                    ctx.stroke();
                }
            }
            ctx.restore();
        }

        // Torres DAC e Partículas de CO₂
        function drawDACTowersAndParticles() {
            // Desenhar Torres
            state.dacTowers.forEach(t => t.draw());

            // Gerar e atualizar partículas de CO₂
            spawnCO2Particle();

            for (let i = state.co2Particles.length - 1; i >= 0; i--) {
                const p = state.co2Particles[i];
                p.progress += 0.02;

                const startX = p.x;
                const startY = p.y;
                const endX = p.targetTower.x;
                const endY = p.targetTower.y - 12;

                const currX = startX + (endX - startX) * p.progress;
                const currY = startY + (endY - startY) * p.progress;

                ctx.save();
                ctx.beginPath();
                ctx.arc(currX, currY, 2, 0, Math.PI * 2);
                ctx.fillStyle = "rgba(168, 85, 247, 0.8)";
                ctx.shadowColor = "#a855f7";
                ctx.shadowBlur = 4;
                ctx.fill();
                ctx.restore();

                if (p.progress >= 1) {
                    state.co2Particles.splice(i, 1);
                }
            }
        }

        // Nuvens e Irrigação Pluvial
        function updateAndDrawClouds() {
            state.cloudClusters.forEach(cloud => {
                cloud.rainActive = state.cloudSeedingActive;
                cloud.update();
                cloud.draw();
            });
        }

        // Gerenciamento e Dissipação de Furacões (Ionização)
        function manageStorms() {
            // Criar novos furacões aleatoriamente
            if (Math.random() < state.stormSpawnRate && state.storms.length < 3) {
                state.storms.push(new Storm());
            }

            for (let i = state.storms.length - 1; i >= 0; i--) {
                const storm = state.storms[i];
                storm.beingNeutralized = false;

                // Se o sistema Ionizador estiver ativo, localizar a estação mais próxima para disparar feixe
                if (state.ionizerActive) {
                    let nearestStation = null;
                    let minDist = Infinity;

                    state.ionStations.forEach(st => {
                        const d = Math.hypot(st.x - storm.x, st.y - storm.y);
                        if (d < minDist) {
                            minDist = d;
                            nearestStation = st;
                        }
                    });

                    // Disparar Feixe Ionizador se estiver ao alcance
                    if (nearestStation && minDist < 600) {
                        storm.beingNeutralized = true;
                        storm.intensity -= 0.008; // Enfraquece o furacão
                        storm.radius = Math.max(10, storm.radius - 0.15);

                        // Desenhar Feixe de Ionização Desintegrador
                        ctx.save();
                        ctx.beginPath();
                        ctx.moveTo(nearestStation.x, nearestStation.y);
                        ctx.lineTo(storm.x, storm.y);
                        
                        const beamGrad = ctx.createLinearGradient(nearestStation.x, nearestStation.y, storm.x, storm.y);
                        beamGrad.addColorStop(0, "rgba(0, 242, 254, 0.9)");
                        beamGrad.addColorStop(1, "rgba(255, 42, 109, 0.4)");

                        ctx.strokeStyle = beamGrad;
                        ctx.lineWidth = 2.5;
                        ctx.shadowColor = "#00f2fe";
                        ctx.shadowBlur = 12;
                        ctx.stroke();
                        ctx.restore();
                    }
                }

                storm.update();
                storm.draw();

                // Se o furacão for completamente neutralizado
                if (storm.intensity <= 0.15 || storm.radius <= 12) {
                    state.storms.splice(i, 1);
                    state.neutralizedCount++;
                    document.getElementById('eventsNeutralized').innerText = state.neutralizedCount;
                }
            }
        }

        function updateAndDrawIonizers() {
            if (state.ionStations.length === 0) initInfrastructure();
            state.ionStations.forEach(st => st.draw());
        }

        // Cálculo do Equilíbrio do Ecossistema em Tempo Real
        function updateEnvironmentMetrics() {
            // Anomalia Térmica cai com Injeção de Aerossóis
            const targetTemp = 1.8 - (state.aerosolReflectivity * 1.2) - (state.dacPower * 0.3);
            state.globalTempAnomaly += (targetTemp - state.globalTempAnomaly) * 0.01;

            // Concentração de CO₂ cai com Torres DAC ativas
            const dacReductionRate = state.dacPower * 0.015;
            state.co2Level = Math.max(350.0, state.co2Level - dacReductionRate + 0.002);

            // Produtividade Agrícola otimizada por chuvas controladas e clima estável
            const tempFactor = Math.max(0, 100 - (state.globalTempAnomaly * 8));
            const rainFactor = state.cloudSeedingActive ? 20 : 5;
            state.cropYieldPct = Math.min(99.9, tempFactor + rainFactor);

            // Atualizar UI DOM
            document.getElementById('tempValue').innerText = `${state.globalTempAnomaly > 0 ? '+' : ''}${state.globalTempAnomaly.toFixed(2)} °C`;
            document.getElementById('co2Value').innerText = `${state.co2Level.toFixed(1)} ppm`;
            document.getElementById('cropYield').innerText = `${state.cropYieldPct.toFixed(1)} %`;
        }

        // Inicialização
        initInfrastructure();
        animate();
    </script>
</body>
</html>
