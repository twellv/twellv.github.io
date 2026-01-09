
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>SW - Rune Viewer Ultimate</title>
    <style>
        /* 1. VARIÁVEIS E BASE */
        :root {
            --bg-dark: #0a0a0c;
            --neon-blue: #00f3ff;
            --neon-pink: #ff00ff;
            --neon-yellow: #fdee06;
            --neon-green: #05ffa1;
            --neon-red: #ff003c;

            --tier-diamante: #00f3ff;

            --tier-platina: #8a2be2; /* BlueViolet - Roxo vibrante */
            --platina-inner: rgba(138, 43, 226, 0.15);
            --platina-glow: #2ce6d4;

            --tier-ouro: #ffcc00;

            --tier-prata: #b0b0b0;

            --tier-bronze: #cd7f32;

            --tier-weak: #ff003c;

            --status-great: #00ff88;
            --status-ok: #f1c40f;
            --status-bad: #e74c3c;
            --status-info: #3498db;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #0b0b0b;
            color: #e0e0e0;
            margin: 0;
            padding: 20px;
            /* IMPORTANTE: Garante que o conteúdo comece depois da sidebar lateral */
            margin-left: 240px;
        }

        h1 {
            color: #ff0033;
            text-align: center;
            text-transform: uppercase;
            letter-spacing: 2px;
            border-bottom: 2px solid #333;
            padding-bottom: 10px;
        }

        /* 2. SIDEBAR LATERAL (FIXA) */
        .sidebar {
            position: fixed;
            left: 20px;
            top: 50%;
            transform: translateY(-50%);
            width: 180px;
            background: #1a1a1a;
            border: 1px solid #333;
            border-radius: 12px;
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 15px;
            z-index: 1000;
        }

        .side-stat-box {
            text-align: center; padding: 10px;
            border-radius: 8px; background: #222;
            border-left: 4px solid #444;
        }
        .side-high { border-left-color: var(--status-great) !important; color: var(--status-great); }
        .side-mid { border-left-color: var(--status-ok) !important; color: var(--status-ok); }
        .side-low { border-left-color: var(--status-bad) !important; color: var(--status-bad); }

        /* 3. GRID DE BOTÕES DE SET (5 COLUNAS) */
        #set-buttons-container {
            display: grid !important;
            grid-template-columns: repeat(10, 55px) !important; /* 10 colunas de 55px */
            gap: 8px; /* Espaço entre botões */
            padding: 15px;
            background: #0f0f13;
            border: 1px solid #333;
            margin: 10px auto;
            width: fit-content;
            justify-content: center;
            border-radius: 12px;
        }

        .rune-set-btn {
            width: 55px; height: 55px;
            background: #1a1a20; border: 2px solid #444;
            cursor: pointer; display: flex; align-items: center; justify-content: center;
            clip-path: polygon(10% 0, 100% 0, 100% 70%, 90% 100%, 0 100%, 0% 30%);
            transition: 0.2s;
        }
        .rune-set-btn img { width: 70%; height: 70%; object-fit: contain; }

        /* Cores dos Sets */
        .rune-clr-fatal  { color: var(--neon-red); border-color: var(--neon-red); }
        .rune-clr-swift  { color: var(--neon-blue); border-color: var(--neon-blue); }
        .rune-clr-energy { color: var(--neon-green); border-color: var(--neon-green); }
        .rune-clr-blade  { color: var(--neon-yellow); border-color: var(--neon-yellow); }
        .rune-clr-rage   { color: var(--neon-pink); border-color: var(--neon-pink); }
        .rune-set-btn.active { box-shadow: 0 0 15px currentColor; border-width: 3px; }

        /* 4. SCOREBOARD (REVISADO E UNIFICADO) */
        .slot-scoreboard {
            display: flex;
            justify-content: center;
            gap: 12px;
            margin-bottom: 25px;
            background: #111;
            padding: 20px;
            border-radius: 12px;
            border: 1px solid #222;
        }

        .slot-box {
            width: 75px;
            height: 95px;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border-radius: 10px;
            background: #1a1a1a;
            border: 2px solid #333;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            box-sizing: border-box; /* Garante que a borda não mude o tamanho */
        }

        .slot-box:hover {
            transform: scale(1.05);
            background: #2a2a2a;
        }

        /* Cores de Ranking - IMPORTANTE: Usar !important para garantir a exibição */
        .bg-rank-diamante { border-color: var(--tier-diamante) !important; color: var(--tier-diamante) !important; box-shadow: inset 0 0 10px rgba(0, 243, 255, 0.2); }
        .bg-rank-platina  { border-color: var(--tier-platina) !important; color: var(--tier-platina) !important; }
        .bg-rank-ouro     { border-color: var(--tier-ouro) !important; color: var(--tier-ouro) !important; }
        .bg-rank-prata    { border-color: var(--tier-prata) !important; color: var(--tier-prata) !important; }
        .bg-rank-bronze   { border-color: var(--tier-bronze) !important; color: var(--tier-bronze) !important; }
        .bg-rank-weak     { border-color: var(--tier-weak) !important; color: var(--tier-weak) !important; border-style: dashed !important; }

        /* Estado Selecionado (Brilho Neon Vermelho) */
        .slot-box.selected {
            border-color: var(--neon-red) !important;
            box-shadow: 0 0 15px rgba(255, 0, 60, 0.5);
            transform: translateY(-5px);
            background: rgba(255, 0, 60, 0.1);
            z-index: 2;
        }

        .slot-label { font-size: 0.65rem; text-transform: uppercase; opacity: 0.7; margin-bottom: 4px; font-weight: bold; pointer-events: none;}
        .slot-number { font-size: 1.4rem; font-weight: 900; pointer-events: none;}

        .slot-all { border-style: double !important; }

        /* 5. GRID DOS CARDS DE RUNAS */
        .rune-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
            gap: 20px;
            width: 100%;
            margin-top: 20px;
        }

        /* 7. ESTILIZAÇÃO PREMIUM DOS CARDS */
        .rune-card {
            background: linear-gradient(145deg, #1a1a1a, #121212);
            border: 1px solid #333;
            border-radius: 12px;
            padding: 18px;
            position: relative;
            transition: all 0.3s ease;
            border-left: 6px solid #444; /* Borda padrão */
            overflow: hidden;
        }

        .rune-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.5);
            border-color: #555;
        }

        /* Animação do brilho passando */
        @keyframes shimmer-diamond {
            0% { transform: translateX(-150%) skewX(-25deg); }
            50% { transform: translateX(150%) skewX(-25deg); }
            100% { transform: translateX(150%) skewX(-25deg); }
        }

        /* Animação da pulsação da borda */
        @keyframes border-glow-diamond {
            0% { box-shadow: inset 0 0 15px rgba(0, 243, 255, 0.2), 0 0 5px rgba(185, 242, 255, 0.1); }
            50% { box-shadow: inset 0 0 25px rgba(0, 243, 255, 0.4), 0 0 15px rgba(185, 242, 255, 0.3); }
            100% { box-shadow: inset 0 0 15px rgba(0, 243, 255, 0.2), 0 0 5px rgba(185, 242, 255, 0.1); }
        }

        .tier-diamante {
            position: relative;
            overflow: hidden; /* Importante para o brilho não sair do card */
            border: 1px solid rgba(185, 242, 255, 0.3) !important;
            border-left: 6px solid #b9f2ff !important;
            background: linear-gradient(135deg, #0f171e 0%, #121212 100%) !important;
            animation: border-glow-diamond 4s infinite ease-in-out;
        }

        /* O "Brilho Mágico" que passa pelo card (igual ao LoL) */
        .tier-diamante::before {
            content: "";
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(
                to right,
                transparent,
                rgba(255, 255, 255, 0) 0%,
                rgba(185, 242, 255, 0.2) 50%,
                transparent 100%
            );
            animation: shimmer-diamond 2s infinite;
            z-index: 1;
            pointer-events: none;
        }

        /* Deixando o título da runa Diamante mais "místico" */
        .tier-diamante .set-name {
            color: #b9f2ff !important;
            text-shadow: 0 0 8px rgba(185, 242, 255, 0.6);
        }

        /* Badge de Tier específica para Diamante */
        .tier-diamante .tier-badge {
            background: rgba(0, 243, 255, 0.2);
            color: #b9f2ff;
            border: 1px solid #b9f2ff;
            box-shadow: 0 0 5px rgba(185, 242, 255, 0.5);
        }

        /* TIER PLATINA - Roxo Challenger Style */
        .tier-platina {
            border-left-color: var(--tier-platina) !important;
            /* Gradiente roxo profundo misturado com o preto do card */
            background: linear-gradient(145deg, rgba(138, 43, 226, 0.12) 0%, #121212 100%) !important;

            /* Brilho interno roxo para dar volume */
            box-shadow:
                inset 0 0 20px rgba(138, 43, 226, 0.1),
                inset 5px 0 10px rgba(138, 43, 226, 0.15) !important;

            border-right: 1px solid rgba(138, 43, 226, 0.05);
        }

        /* Deixando o texto do título da Platina combinando com o novo Roxo */
        .tier-platina .set-name {
            color: #b388ff !important; /* Um lilás claro para leitura */
            text-shadow: 0 0 5px rgba(138, 43, 226, 0.3);
        }

        /* Badge da Platina (Roxa) */
        .tier-platina .tier-badge {
            color: #d1b3ff;
            border-color: #8a2be2;
            background: rgba(138, 43, 226, 0.1);
        }

        .tier-ouro {
            border-left-color: var(--tier-ouro) !important;
            background: linear-gradient(145deg, rgba(255, 204, 0, 0.08), #121212) !important;
            box-shadow: inset 0 0 20px rgba(255, 204, 0, 0.05);
        }

        /* TIER PRATA (Prateado) */
        .tier-prata {
            border-left-color: var(--tier-prata) !important;
            /* Opacidade bem fraca no fundo (0.05) para parecer metal polido fosco */
            background: linear-gradient(145deg, rgba(176, 176, 176, 0.05), #121212) !important;
            box-shadow: inset 0 0 15px rgba(176, 176, 176, 0.03);
            border-right: 1px solid rgba(176, 176, 176, 0.1); /* Sutil brilho na borda oposta */
        }

        .tier-prata .tier-badge {
            color: #ccc;
            border-color: #666;
            background: rgba(255, 255, 255, 0.02);
        }

        /* TIER BRONZE (Acobreado) */
        .tier-bronze {
            border-left-color: var(--tier-bronze) !important;
            background: linear-gradient(145deg, rgba(205, 127, 50, 0.08), #121212) !important;
        }

        .tier-weak {
            border-left-color: var(--tier-weak) !important;
            background: linear-gradient(145deg, #1a0a0a, #121212);
            opacity: 0.9;
        }

        /* Detalhes Internos do Card (Nível e Status) */
        .header-main-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: rgba(0, 0, 0, 0.3);
            padding: 8px 12px;
            border-radius: 6px;
            margin: 10px 0;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }

        .main-stat-text {
            color: var(--neon-blue);
            font-weight: 800;
            text-transform: uppercase;
            font-size: 0.95rem;
            text-shadow: 0 0 8px rgba(0, 243, 255, 0.4);
        }

        .rune-level {
            background: #ffcc00;
            color: #000;
            padding: 2px 8px;
            border-radius: 4px;
            font-weight: 900;
            font-size: 0.8rem;
            box-shadow: 0 0 10px rgba(255, 204, 0, 0.3);
        }

        .rune-level-max {
            background: #00ff88 !important; /* Verde Esmeralda */
            color: #000 !important;
            padding: 2px 8px;
            border-radius: 4px;
            font-weight: 900;
            font-size: 0.8rem;
            /* Brilho reduzido: de 12px para 5px e com menos opacidade */
            box-shadow: 0 0 8px rgba(0, 255, 136, 0.4);
            text-shadow: none; /* Remove sombras internas no texto que cansam a vista */
        }

        /* Substats List */
        .substats-list li {
            padding: 6px 0;
            font-size: 0.85em;
            border-bottom: 1px solid rgba(255, 255, 255, 0.03);
            color: #bbb;
        }

        /* Cores de Velocidade (SPD) dentro dos substats */
        .spd-high { color: var(--status-great); font-weight: bold; text-shadow: 0 0 5px rgba(0, 255, 136, 0.2); }
        .spd-mid { color: var(--status-ok); font-weight: bold; }
        .spd-low { color: #888; }

        /* Estilo para o Nome do Set (Fatal, Swift, etc) */
        .set-name {
            font-size: 1.1rem;
            font-weight: bold;
            color: #fff;
            margin-bottom: 5px;
            display: block;
        }

        .rune-card {
            background: #181818; border: 1px solid #333;
            border-radius: 10px; padding: 15px; border-left: 6px solid #444;
        }

        /* Tiers nos Cards */
        .tier-diamante { border-left-color: var(--tier-diamante) !important; }
        .tier-platina  { border-left-color: var(--tier-platina) !important; }
        .tier-ouro     { border-left-color: var(--tier-ouro) !important; }
        .tier-prata    { border-left-color: var(--tier-prata) !important; }
        .tier-bronze   { border-left-color: var(--tier-bronze) !important; }
        .tier-weak     { border-left-color: var(--tier-weak) !important; background: rgba(255, 0, 60, 0.05); }

        /* 6. CONTROLES E BOTÕES DE SLOT */
        .controls {
            background: #1a1a1a; padding: 20px; border-radius: 8px;
            margin-bottom: 20px; border: 1px solid #333;
            display: flex; flex-wrap: wrap; gap: 20px; justify-content: center;
        }
        .btn-slot { width: 35px; height: 35px; cursor: pointer; background: #252525; color: white; border: 1px solid #444; border-radius: 4px; }
        .btn-slot.active { background: #ff0033; border-color: #ff0033; }

        /* Placar de estatísticas (Total, Inv, Eq) */
        #count-inv { color: var(--status-great) !important; font-weight: bold; }
        #count-eq { color: var(--status-info) !important; font-weight: bold; }

        /* Container Principal: Agora alinhado horizontalmente */
        .stats-container {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin: 20px 0;
            padding: 10px;
        }

        /* Caixa de Estatística Individual */
        .stat-box {
            flex: 1;
            max-width: 220px;
            background: linear-gradient(145deg, #1e1e24, #111114);
            border: 1px solid #333;
            border-radius: 12px;
            padding: 15px 20px;
            display: flex; /* Alinha ícone e texto lado a lado */
            align-items: center;
            gap: 15px;
            transition: all 0.3s ease;
            position: relative;
            border-bottom: 3px solid #333; /* Borda colorida embaixo */
        }

        /* Títulos (Total, Inventário, etc) */
        .stat-box h3 {
            margin: 0;
            font-size: 0.7rem;
            text-transform: uppercase;
            color: #888;
            letter-spacing: 1px;
        }

        /* Números (Os contadores) */
        .stat-box p {
            margin: 0;
            font-size: 1.6rem;
            font-weight: 900;
            line-height: 1.1;
        }

        .stat-box:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 25px rgba(0,0,0,0.6);
            background: #1c1c21;
        }

        /* Cores específicas para cada box com brilho neon sutil */
        #count-total { color: #ffffff; text-shadow: 0 0 10px rgba(255, 255, 255, 0.3); }
        #count-inv   { color: #a29bfe; text-shadow: 0 0 10px rgba(162, 155, 254, 0.4); }
        #count-eq    { color: #fab1a0; text-shadow: 0 0 10px rgba(250, 177, 160, 0.4); }

        /* Pequena barra decorativa no topo de cada box */
        .stat-box::before {
            content: '';
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 3px;
            background: #333;
        }

        /* Cores das bordas inferiores para identificar rápido */
        .stat-box:nth-child(1) { border-bottom-color: #555; }
        .stat-box:nth-child(2) { border-bottom-color: #a29bfe; }
        .stat-box:nth-child(3) { border-bottom-color: #fab1a0; }

        .stat-icon {
        font-size: 1.8rem;
        filter: drop-shadow(0 0 5px rgba(0,0,0,0.5));
       }

        .stat-content {
            text-align: left;
        }

        /* Container da Legenda */
        .legend-container {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 25px;
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.1);
            padding: 12px 25px;
            border-radius: 50px; /* Formato de pílula */
            margin: 20px auto;
            width: fit-content;
            backdrop-filter: blur(5px);
        }

        /* Item individual da legenda */
        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.85rem;
            font-weight: 600;
            color: #bbb;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        /* Os pontos coloridos (Dots) */
        .dot {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            position: relative;
        }

        /* Cores e Brilhos Neon para os Dots */
        .dot-diamante { background-color: var(--tier-diamante); box-shadow: 0 0 8px var(--tier-diamante); }
        .dot-platina  { background-color: var(--tier-platina);  box-shadow: 0 0 8px var(--tier-platina); }
        .dot-ouro     { background-color: var(--tier-ouro);     box-shadow: 0 0 8px var(--tier-ouro); }
        .dot-prata    { background-color: var(--tier-prata);    box-shadow: 0 0 8px var(--tier-prata); }
        .dot-bronze   { background-color: var(--tier-bronze);   box-shadow: 0 0 8px var(--tier-bronze); }

        /* Pequeno ajuste para o texto não ficar colado */
        .legend-item b {
            color: #fff;
            margin-right: 2px;
        }

        .tier-filter-bar {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 12px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .tier-btn {
            background: #1a1a1a;
            border: 1px solid #333;
            color: #888;
            padding: 6px 15px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 0.85rem;
            font-weight: bold;
            transition: all 0.2s;
        }

        /* Cores Ativas de cada botão */
        .tier-btn.active.btn-all { border-color: #fff; color: #fff; box-shadow: 0 0 10px rgba(255,255,255,0.2); }
        .tier-btn.active.btn-dia { border-color: #b9f2ff; color: #b9f2ff; box-shadow: 0 0 10px rgba(185,242,255,0.3); }
        .tier-btn.active.btn-pla { border-color: #8a2be2; color: #8a2be2; box-shadow: 0 0 10px rgba(138,43,226,0.3); }
        .tier-btn.active.btn-ouro { border-color: #ffcc00; color: #ffcc00; box-shadow: 0 0 10px rgba(255,204,0,0.3); }
        .tier-btn.active.btn-prata { border-color: var(--tier-prata); color: var(--tier-prata); box-shadow: 0 0 10px rgba(176, 176, 176, 0.3); }
        .tier-btn.active.btn-weak  { border-color: var(--tier-weak); color: var(--tier-weak); box-shadow: 0 0 10px rgba(255, 0, 60, 0.3); }
        .tier-btn.active.loc-btn {
            border-color: var(--status-info);
            color: var(--status-info);
            box-shadow: 0 0 10px rgba(52, 152, 219, 0.3);
        }

        .tier-btn:hover {
            transform: translateY(-2px);
            background: #222;
        }

        .main-sort-bar {
            background: rgba(0, 0, 0, 0.2);
            padding: 15px;
            border-radius: 8px;
            border: 1px solid #222;
            margin-bottom: 25px;
        }

        .sort-btn-main {
            background: #1a1a1a;
            border: 1px solid #444;
            color: #888;
            padding: 6px 12px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 0.8rem;
            font-weight: bold;
            transition: 0.2s;
        }

        .sort-btn-main:hover {
            border-color: var(--neon-blue);
            color: #fff;
        }

        .sort-btn-main.active {
            background: var(--neon-blue);
            color: #000;
            border-color: var(--neon-blue);
            box-shadow: 0 0 10px rgba(0, 243, 255, 0.3);
        }
    </style>
</head>
<body>

<h1>Summoners War - Rune Viewer Ultimate</h1>

<button onclick="window.open('selling.html', '_blank')" class="btn-sell" style="background: var(--neon-red); color: white; padding: 10px 20px; border: none; cursor: pointer; font-weight: bold; border-radius: 5px; box-shadow: 0 0 15px var(--neon-red); margin: 10px;">
    MODO FAXINA (WEAK ONLY) 🧹
</button>

<div class="sidebar">
    <div class="sidebar-title">Global Quality</div>

    <div class="side-stat-box side-high">
        <span>Total High</span>
        <strong id="global-high">0</strong>
    </div>

    <div class="side-stat-box side-mid">
        <span>Total Mid</span>
        <strong id="global-mid">0</strong>
    </div>

    <div class="side-stat-box side-low">
        <span>Total Low</span>
        <strong id="global-low">0</strong>
    </div>
</div>

<div class="rune-inventory">
    <h3 class="inventory-title">RUNE DECK_</h3>
    <div class="rune-grid" id="rune-container">
    </div>
</div>
<div class="filter-group">
    <label>1. Arquivo</label>
    <input type="file" id="fileInput" accept=".json">
</div>
<div class="controls">
    <div class="filter-group">
        <label>2. Sets (Ctrl+Click)</label>
        <div id="set-buttons-container">
        </div>
    </div>
</div>
<div class="legend-container">
    <div class="legend-item"><div class="dot dot-diamante"></div> 4+ Diamante</div>
    <div class="legend-item"><div class="dot dot-platina"></div> 8+ Platina</div>
    <div class="legend-item"><div class="dot dot-ouro"></div> 12+ Ouro</div>
    <div class="legend-item"><div class="dot dot-prata"></div> 8+ Prata</div>
    <div class="legend-item"><div class="dot dot-bronze"></div> 4+ Bronze</div>
</div>
<div class="slot-scoreboard" id="scoreboard">
    <div class="slot-box slot-all selected" id="slot-all" onclick="selectSlot('all')">
        <span class="slot-label">Visualizar</span>
        <span class="slot-number">ALL</span>
    </div>
    <div class="slot-box bg-none" id="slot-1" onclick="selectSlot('1')"><span class="slot-label">Slot</span><span class="slot-number">1</span></div>
    <div class="slot-box bg-none" id="slot-2" onclick="selectSlot('2')"><span class="slot-label">Slot</span><span class="slot-number">2</span></div>
    <div class="slot-box bg-none" id="slot-3" onclick="selectSlot('3')"><span class="slot-label">Slot</span><span class="slot-number">3</span></div>
    <div class="slot-box bg-none" id="slot-4" onclick="selectSlot('4')"><span class="slot-label">Slot</span><span class="slot-number">4</span></div>
    <div class="slot-box bg-none" id="slot-5" onclick="selectSlot('5')"><span class="slot-label">Slot</span><span class="slot-number">5</span></div>
    <div class="slot-box bg-none" id="slot-6" onclick="selectSlot('6')"><span class="slot-label">Slot</span><span class="slot-number">6</span></div>
</div>

<div class="stats-container">
    <div class="stat-box">
        <div class="stat-icon">📊</div>
        <div class="stat-content">
            <h3>Total</h3>
            <p id="count-total">0</p>
        </div>
    </div>
    <div class="stat-box">
        <div class="stat-icon">📦</div>
        <div class="stat-content">
            <h3>Inventário</h3>
            <p id="count-inv">0</p>
        </div>
    </div>
    <div class="stat-box">
        <div class="stat-icon">⚔️</div>
        <div class="stat-content">
            <h3>Equipadas</h3>
            <p id="count-eq">0</p>
        </div>
    </div>
</div>

<div class="tier-filter-bar">
    <span style="color: #666; font-size: 0.8rem; text-transform: uppercase; font-weight: bold;">Filtrar Qualidade:</span>
    <button class="tier-btn btn-all active" onclick="filterByTier('all', this)">Todas</button>
    <button class="tier-btn btn-dia" onclick="filterByTier('Diamante', this)">Diamante</button>
    <button class="tier-btn btn-pla" onclick="filterByTier('Platina', this)">Platina</button>
    <button class="tier-btn btn-ouro" onclick="filterByTier('Ouro', this)">Ouro</button>
    <button class="tier-btn btn-prata" onclick="filterByTier('Prata', this)">Prata</button>
    <button class="tier-btn btn-weak" onclick="filterByTier('Weak', this)">Weak</button>
</div>

<div class="tier-filter-bar location-filter-bar">
    <span style="color: #666; font-size: 0.8rem; text-transform: uppercase; font-weight: bold;">Localização:</span>
    <button class="tier-btn loc-btn active" onclick="filterByLocation('all', this)">Todos</button>
    <button class="tier-btn loc-btn" onclick="filterByLocation('Inventário', this)">No Inventário</button>
    <button class="tier-btn loc-btn" onclick="filterByLocation('Equipadas', this)">Equipadas</button>
</div>

<div class="tier-filter-bar main-stat-filter-bar">
    <span style="color: #666; font-size: 0.8rem; text-transform: uppercase; font-weight: bold;">Atributo Principal:</span>
    <button class="tier-btn main-stat-btn active" onclick="filterByMainStat('all', this)">Todos</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('SPD', this)">SPD</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('HP%', this)">HP%</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('ATK%', this)">ATK%</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('DEF%', this)">DEF%</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('CRIT Rate', this)">CR</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('CRIT Damage', this)">CD</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('ACC', this)">ACC</button>
    <button class="tier-btn main-stat-btn" onclick="filterByMainStat('RES', this)">RES</button>
</div>

<div class="tier-filter-bar main-sort-bar">
    <span style="color: #666; font-size: 0.8rem; text-transform: uppercase; font-weight: bold;">Ordenar Cards por:</span>
    <button class="sort-btn-main active" onclick="setMainSort('SPD', this)">SPD</button>
    <button class="sort-btn-main" onclick="setMainSort('HP%', this)">HP%</button>
    <button class="sort-btn-main" onclick="setMainSort('ATK%', this)">ATK%</button>
    <button class="sort-btn-main" onclick="setMainSort('DEF%', this)">DEF%</button>
    <button class="sort-btn-main" onclick="setMainSort('CRIT Rate', this)">CR</button>
    <button class="sort-btn-main" onclick="setMainSort('CRIT Damage', this)">CD</button>
    <button class="sort-btn-main" onclick="setMainSort('ACC', this)">ACC</button>
    <button class="sort-btn-main" onclick="setMainSort('RES', this)">RES</button>
</div>

<div id="active-filters-bar" style="margin: 10px 0; padding: 10px; background: #151515; border-radius: 8px; border-left: 4px solid var(--neon-red); display: none; align-items: center; gap: 15px;">
    <span style="font-size: 0.8rem; color: #888; text-transform: uppercase; font-weight: bold;">Filtros Ativos:</span>
    <div id="filter-badges-container" style="display: flex; gap: 10px; flex-wrap: wrap;">
    </div>
    <button onclick="clearAllFilters()" style="margin-left: auto; background: transparent; border: 1px solid #444; color: #aaa; cursor: pointer; padding: 4px 8px; border-radius: 4px; font-size: 0.75rem; transition: 0.2s;">
        Limpar Tudo
    </button>
</div>

<div id="runeContainer" class="rune-container"></div>

<script>
    // 1. VARIÁVEIS GLOBAIS (Declaradas apenas uma vez)
    let allRunes = [];
    let currentMainSort = 'SPD';
    let currentSelectedSlot = 'all';
    let currentSelectedTier = 'all';
    let selectedSets = [];
    let currentMainStatFilter = 'all';
    let currentLocFilter = 'all';

    // Mapeamento de cores para os botões de Set
    const setColors = {
        "Fatal": "fatal",
        "Swift": "swift",
        "Energy": "energy",
        "Blade": "blade",
        "Rage": "rage",
        "Violent": "swift",
        "Will": "rage"
    };

    // 2. CARREGAMENTO DO ARQUIVO
    document.getElementById('fileInput').addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = (e) => {
            try {
                const conteudo = e.target.result;
                allRunes = JSON.parse(conteudo);

                localStorage.setItem('meu_json_runas', conteudo);

                populateSetFilter();
                applyFilters();
            } catch (err) {
                console.error("Erro ao ler JSON:", err);
                alert("Erro ao processar o arquivo JSON.");
            }
        };
        reader.readAsText(file);
    });

    // 3. FUNÇÕES DE SUPORTE
    function getSpdValue(subs) {
        const line = subs.find(s => s.toUpperCase().includes('SPD'));
        if (!line) return -1;
        const match = line.match(/SPD:\s*(\d+)/i);
        return match ? parseInt(match[1]) : -1;
    }

    function populateSetFilter() {
        const container = document.getElementById('set-buttons-container');
        const uniqueSets = [...new Set(allRunes.map(r => r.set_name))].sort();
        container.innerHTML = '';

        uniqueSets.forEach(setName => {
            const btn = document.createElement('button');
            const colorClass = setColors[setName] || 'default';
            btn.className = `rune-set-btn rune-clr-${colorClass}`;

            const img = document.createElement('img');
            img.src = `${setName}.webp`;
            img.alt = setName;
            img.style.pointerEvents = 'none';

            img.onerror = () => {
                btn.innerHTML = setName.substring(0, 2).toUpperCase();
            };

            btn.appendChild(img);
            btn.title = setName;
            btn.onclick = (e) => toggleSetFilter(setName, btn, e);
            container.appendChild(btn);
        });
    }

    function toggleSetFilter(setName, btn, event) {
        const isCtrlPressed = event.ctrlKey;
        if (isCtrlPressed) {
            if (selectedSets.includes(setName)) {
                selectedSets = selectedSets.filter(s => s !== setName);
                btn.classList.remove('active');
            } else {
                selectedSets.push(setName);
                btn.classList.add('active');
            }
        } else {
            if (selectedSets.length === 1 && selectedSets.includes(setName)) {
                selectedSets = [];
                btn.classList.remove('active');
            } else {
                document.querySelectorAll('.rune-set-btn').forEach(b => b.classList.remove('active'));
                selectedSets = [setName];
                btn.classList.add('active');
            }
        }
        applyFilters();
    }

    // 4. LÓGICA PRINCIPAL (FILTRO E ORDENAÇÃO)
    function applyFilters() {
        const filtered = allRunes.filter(rune => {
            const matchSlot = (currentSelectedSlot === 'all' || rune.slot.toString() === currentSelectedSlot);
            const matchSet = (selectedSets.length === 0 || selectedSets.includes(rune.set_name));
            const matchTier = (currentSelectedTier === 'all' || rune.tier === currentSelectedTier);
            const matchMainStat = (currentMainStatFilter === 'all' ||
                               rune.principal.toUpperCase().includes(currentMainStatFilter.toUpperCase()));

            let matchLocation = true;
            if (currentLocFilter === 'Inventário') {
                matchLocation = rune.local === 'Inventário';
            } else if (currentLocFilter === 'Equipadas') {
                matchLocation = rune.local !== 'Inventário';
            }

            return matchSlot && matchSet && matchTier && matchMainStat && matchLocation;
        });

        const getStatValue = (rune, statName) => {
            const sub = rune.substatus.find(s => s.toUpperCase().includes(statName.toUpperCase()));
            if (!sub) return -1;
            const match = sub.match(/:?\s*(\d+)/);
            return match ? parseInt(match[1]) : -1;
        };

        const sortedRunes = [...filtered].sort((a, b) => {
            return getStatValue(b, currentMainSort) - getStatValue(a, currentMainSort);
        });

        // Stats Sidebar
        const highCount = filtered.filter(r => r.tier === "Diamante" || r.tier === "Platina").length;
        const midCount = filtered.filter(r => r.tier === "Ouro" || r.tier === "Prata").length;
        const lowCount = filtered.filter(r => r.tier === "Bronze" || r.tier === "Weak").length;

        if(document.getElementById('global-high')) document.getElementById('global-high').textContent = highCount;
        if(document.getElementById('global-mid')) document.getElementById('global-mid').textContent = midCount;
        if(document.getElementById('global-low')) document.getElementById('global-low').textContent = lowCount;

        // Stats Totais
        const totalCount = filtered.length;
        const invCount = filtered.filter(r => (r.local || "").toLowerCase().includes('inventário')).length;
        const eqCount = totalCount - invCount;

        if(document.getElementById('count-total')) document.getElementById('count-total').textContent = totalCount;
        if(document.getElementById('count-inv')) document.getElementById('count-inv').textContent = invCount;
        if(document.getElementById('count-eq')) document.getElementById('count-eq').textContent = eqCount;

        updateSlotColors(filtered);
        updateFilterLegend();
        renderRunes(sortedRunes);
    }

    function renderRunes(runes) {
        const container = document.getElementById('runeContainer');
        container.innerHTML = '';

        runes.forEach(rune => {
            const tier = (rune.tier || "Weak").toLowerCase();
            const card = document.createElement('div');
            card.className = `rune-card tier-${tier} ${rune.local === 'Inventário' ? 'card-inventario' : 'card-equipada'}`;

            const runeLevel = rune.upgrade || "+0";
            const levelClass = runeLevel.includes("15") ? 'rune-level-max' : 'rune-level';

            const subsHtml = rune.substatus.map(s => {
                let cls = '';
                if (s.toUpperCase().includes('SPD')) {
                    const v = getSpdValue([s]);
                    cls = v >= 18 ? 'spd-high' : (v >= 12 ? 'spd-mid' : 'spd-low');
                }
                return `<li class="${cls}">${s}</li>`;
            }).join('');

            card.innerHTML = `
                <div class="rune-header">
                    <div class="header-top">
                        <span class="set-name">${rune.set_name} <small style="color:#888;">Slot ${rune.slot}</small></span>
                        <span class="${levelClass}">${runeLevel}</span>
                    </div>
                    <div class="header-main-row">
                        <span class="main-stat-text">${rune.principal}</span>
                        <span class="tier-badge">${rune.tier || "WEAK"}</span>
                    </div>
                </div>
                <ul class="substats-list">${subsHtml}</ul>
                <div class="card-footer"><span class="location-tag">${rune.local}</span></div>
            `;
            container.appendChild(card);
        });
    }

    function selectSlot(slotId) {
        currentSelectedSlot = slotId;
        document.querySelectorAll('.slot-box').forEach(box => box.classList.remove('selected'));
        const targetId = (slotId === 'all') ? 'slot-all' : `slot-${slotId}`;
        document.getElementById(targetId).classList.add('selected');
        applyFilters();
    }

    function updateSlotColors(runes) {
        let slotRanks = [];
        for (let i = 1; i <= 6; i++) {
            const slotEl = document.getElementById(`slot-${i}`);
            if (!slotEl) continue;
            const runesInSlot = runes.filter(r => r.slot === i);
            slotEl.className = 'slot-box';
            if (currentSelectedSlot == i) slotEl.classList.add('selected');
            if (runesInSlot.length === 0) continue;

            const d = runesInSlot.filter(r => r.tier?.toLowerCase() === "diamante").length;
            const p = runesInSlot.filter(r => r.tier?.toLowerCase() === "platina").length;
            const o = runesInSlot.filter(r => r.tier?.toLowerCase() === "ouro").length;
            const s = runesInSlot.filter(r => r.tier?.toLowerCase() === "prata").length;
            const b = runesInSlot.filter(r => r.tier?.toLowerCase() === "bronze").length;

            let currentRank = 'bg-rank-weak';
            if (d >= 3) currentRank = 'bg-rank-diamante';
            else if ((d + p) >= 8) currentRank = 'bg-rank-platina';
            else if ((d + p + o) >= 12) currentRank = 'bg-rank-ouro';
            else if ((d + p + o + s) >= 12) currentRank = 'bg-rank-prata';
            else if ((d + p + o + s + b) > 0) currentRank = 'bg-rank-bronze';

            slotEl.classList.add(currentRank);
            slotRanks.push(currentRank);
        }
    }

    function filterByTier(tier, btn) {
        currentSelectedTier = tier;
        document.querySelectorAll('.tier-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        applyFilters();
    }

    function setMainSort(stat, btn) {
        currentMainSort = stat;
        document.querySelectorAll('.sort-btn-main').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        applyFilters();
    }

    function updateFilterLegend() {
        const bar = document.getElementById('active-filters-bar');
        const container = document.getElementById('filter-badges-container');
        if(!bar || !container) return;
        container.innerHTML = '';
        let hasFilter = (currentSelectedSlot !== 'all' || selectedSets.length > 0);

        if (currentSelectedSlot !== 'all') {
            const badge = document.createElement('span');
            badge.style = "background: #222; border: 1px solid var(--neon-blue); color: var(--neon-blue); padding: 2px 10px; border-radius: 20px; font-size: 0.8rem; font-weight: bold;";
            badge.innerHTML = `Slot ${currentSelectedSlot}`;
            container.appendChild(badge);
        }

        selectedSets.forEach(setName => {
            const badge = document.createElement('span');
            badge.style = "background: #222; border: 1px solid var(--neon-yellow); color: var(--neon-yellow); padding: 2px 10px; border-radius: 20px; font-size: 0.8rem; font-weight: bold;";
            badge.innerHTML = `Set: ${setName}`;
            container.appendChild(badge);
        });

        bar.style.display = hasFilter ? 'flex' : 'none';
    }

    function clearAllFilters() {
        selectedSets = [];
        currentSelectedSlot = 'all';
        currentSelectedTier = 'all';
        document.querySelectorAll('.rune-set-btn').forEach(b => b.classList.remove('active'));
        document.querySelectorAll('.slot-box').forEach(box => box.classList.remove('selected'));
        document.getElementById('slot-all').classList.add('selected');
        document.querySelectorAll('.tier-btn').forEach(b => b.classList.remove('active'));
        document.querySelector('.btn-all').classList.add('active');

        currentMainStatFilter = 'all';
        document.querySelectorAll('.main-stat-btn').forEach(b => b.classList.remove('active'));
        document.querySelector('.main-stat-btn').classList.add('active'); // Reativa o "Todos"

        applyFilters();
    }

    function filterByMainStat(stat, btn) {
        currentMainStatFilter = stat;
        // Atualiza visual dos botões desta barra específica
        document.querySelectorAll('.main-stat-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        applyFilters();
    }

    function filterByLocation(loc, btn) {
        currentLocFilter = loc;
        document.querySelectorAll('.loc-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        applyFilters();
    }
</script>
</body>
</html>
