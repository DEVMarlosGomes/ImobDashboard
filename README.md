<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Imob Dashboard — Marlos Gomes</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;1,9..40,300&display=swap" rel="stylesheet"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  :root {
    --bg:        #0a0b0d;
    --surface:   #111316;
    --surface2:  #181b1f;
    --border:    rgba(255,255,255,0.07);
    --border2:   rgba(255,255,255,0.12);
    --gold:      #c9a84c;
    --gold-dim:  #9b7a2f;
    --gold-glow: rgba(201,168,76,0.15);
    --teal:      #2dd4a0;
    --teal-dim:  rgba(45,212,160,0.12);
    --blue:      #5b9cf6;
    --blue-dim:  rgba(91,156,246,0.12);
    --coral:     #f4745a;
    --coral-dim: rgba(244,116,90,0.12);
    --text:      #e8e6e0;
    --muted:     #6b6960;
    --muted2:    #9a9690;
    --font-serif: 'DM Serif Display', Georgia, serif;
    --font-sans:  'DM Sans', sans-serif;
    --r:         12px;
    --r-lg:      18px;
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  html { scroll-behavior: smooth; }

  body {
    font-family: var(--font-sans);
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    font-size: 14px;
    line-height: 1.6;
    overflow-x: hidden;
  }

  /* grain overlay */
  body::before {
    content: '';
    position: fixed; inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.035'/%3E%3C/svg%3E");
    pointer-events: none; z-index: 0; opacity: 0.4;
  }

  /* ─── LAYOUT ─────────────────────────────────── */
  .shell { display: flex; min-height: 100vh; position: relative; z-index: 1; }

  /* ─── SIDEBAR ────────────────────────────────── */
  .sidebar {
    width: 220px; flex-shrink: 0;
    background: var(--surface);
    border-right: 1px solid var(--border);
    display: flex; flex-direction: column;
    padding: 32px 0;
    position: sticky; top: 0; height: 100vh;
  }

  .logo {
    padding: 0 24px 28px;
    border-bottom: 1px solid var(--border);
    margin-bottom: 28px;
  }
  .logo-mark {
    font-family: var(--font-serif);
    font-size: 22px;
    color: var(--gold);
    letter-spacing: -0.5px;
    line-height: 1.1;
  }
  .logo-sub {
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 0.15em;
    text-transform: uppercase;
    margin-top: 3px;
  }

  .nav { flex: 1; padding: 0 12px; }

  .nav-section {
    font-size: 9px;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: var(--muted);
    padding: 0 12px;
    margin: 20px 0 8px;
  }

  .nav-item {
    display: flex; align-items: center; gap: 10px;
    padding: 9px 12px;
    border-radius: 8px;
    cursor: pointer;
    font-size: 13px;
    color: var(--muted2);
    transition: all 0.2s;
    user-select: none;
    margin-bottom: 2px;
  }
  .nav-item:hover { background: rgba(255,255,255,0.04); color: var(--text); }
  .nav-item.active { background: var(--gold-glow); color: var(--gold); }
  .nav-item .icon { font-size: 15px; width: 18px; text-align: center; }

  .sidebar-footer {
    padding: 20px 24px 0;
    border-top: 1px solid var(--border);
  }
  .avatar-row { display: flex; align-items: center; gap: 10px; }
  .avatar {
    width: 34px; height: 34px; border-radius: 50%;
    background: linear-gradient(135deg, var(--gold-dim), var(--gold));
    display: flex; align-items: center; justify-content: center;
    font-family: var(--font-serif); font-size: 14px; color: #0a0b0d;
    flex-shrink: 0;
  }
  .avatar-info .name { font-size: 13px; font-weight: 500; color: var(--text); }
  .avatar-info .role { font-size: 10px; color: var(--muted); }

  /* ─── MAIN ───────────────────────────────────── */
  .main { flex: 1; overflow-y: auto; padding: 36px 40px; }

  /* ─── HEADER ─────────────────────────────────── */
  .topbar {
    display: flex; align-items: flex-start; justify-content: space-between;
    margin-bottom: 36px;
    opacity: 0; animation: fadeUp 0.6s 0.1s forwards;
  }
  .topbar-title {
    font-family: var(--font-serif);
    font-size: 32px;
    color: var(--text);
    line-height: 1.1;
    margin-bottom: 6px;
  }
  .topbar-sub { font-size: 13px; color: var(--muted2); }

  .period-tabs {
    display: flex; gap: 4px;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 4px;
  }
  .period-tab {
    padding: 5px 14px; border-radius: 5px;
    font-size: 12px; cursor: pointer;
    color: var(--muted2);
    transition: all 0.18s;
    font-family: var(--font-sans);
    border: none; background: none;
  }
  .period-tab.active { background: var(--gold); color: #0a0b0d; font-weight: 500; }

  /* ─── KPI CARDS ──────────────────────────────── */
  .kpi-grid {
    display: grid;
    grid-template-columns: repeat(4, minmax(0,1fr));
    gap: 16px;
    margin-bottom: 28px;
  }

  .kpi-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--r-lg);
    padding: 22px 22px 18px;
    position: relative; overflow: hidden;
    opacity: 0;
    animation: fadeUp 0.55s forwards;
    cursor: default;
    transition: border-color 0.2s, transform 0.2s;
  }
  .kpi-card:hover { border-color: var(--border2); transform: translateY(-2px); }
  .kpi-card::before {
    content: '';
    position: absolute; top: 0; left: 0; right: 0; height: 2px;
    border-radius: var(--r-lg) var(--r-lg) 0 0;
  }
  .kpi-card.gold::before  { background: var(--gold); }
  .kpi-card.teal::before  { background: var(--teal); }
  .kpi-card.blue::before  { background: var(--blue); }
  .kpi-card.coral::before { background: var(--coral); }

  .kpi-icon {
    width: 36px; height: 36px; border-radius: 9px;
    display: flex; align-items: center; justify-content: center;
    font-size: 17px; margin-bottom: 14px;
  }
  .kpi-card.gold .kpi-icon  { background: var(--gold-glow); }
  .kpi-card.teal .kpi-icon  { background: var(--teal-dim); }
  .kpi-card.blue .kpi-icon  { background: var(--blue-dim); }
  .kpi-card.coral .kpi-icon { background: var(--coral-dim); }

  .kpi-label { font-size: 11px; color: var(--muted); letter-spacing: 0.05em; margin-bottom: 5px; }
  .kpi-value { font-family: var(--font-serif); font-size: 28px; color: var(--text); line-height: 1; margin-bottom: 8px; }
  .kpi-delta {
    display: inline-flex; align-items: center; gap: 4px;
    font-size: 11px; padding: 2px 7px; border-radius: 20px;
  }
  .kpi-delta.up   { background: rgba(45,212,160,0.1); color: var(--teal); }
  .kpi-delta.down { background: rgba(244,116,90,0.1); color: var(--coral); }

  /* ─── CHART GRID ─────────────────────────────── */
  .chart-grid {
    display: grid;
    grid-template-columns: 2fr 1fr;
    gap: 16px;
    margin-bottom: 28px;
  }

  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--r-lg);
    padding: 24px;
    opacity: 0;
    animation: fadeUp 0.55s forwards;
    transition: border-color 0.2s;
  }
  .card:hover { border-color: var(--border2); }

  .card-header {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 20px;
  }
  .card-title { font-family: var(--font-serif); font-size: 16px; color: var(--text); }
  .card-badge {
    font-size: 10px; padding: 3px 9px; border-radius: 20px;
    background: var(--gold-glow); color: var(--gold);
    letter-spacing: 0.05em;
  }

  canvas { max-width: 100%; }

  /* ─── BOTTOM GRID ────────────────────────────── */
  .bottom-grid {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 16px;
  }

  /* ─── LEADS TABLE ────────────────────────────── */
  .leads-list { display: flex; flex-direction: column; gap: 0; }
  .lead-row {
    display: flex; align-items: center; gap: 12px;
    padding: 11px 0;
    border-bottom: 1px solid var(--border);
    cursor: pointer;
    transition: padding-left 0.15s;
  }
  .lead-row:last-child { border-bottom: none; }
  .lead-row:hover { padding-left: 4px; }
  .lead-dot { width: 8px; height: 8px; border-radius: 50%; flex-shrink: 0; }
  .lead-info { flex: 1; min-width: 0; }
  .lead-name { font-size: 13px; font-weight: 500; color: var(--text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .lead-type { font-size: 11px; color: var(--muted); }
  .lead-value { font-size: 13px; color: var(--gold); font-weight: 500; white-space: nowrap; }
  .lead-status {
    font-size: 10px; padding: 2px 8px; border-radius: 20px;
    white-space: nowrap;
  }
  .status-quente  { background: rgba(244,116,90,0.12); color: var(--coral); }
  .status-morno   { background: rgba(201,168,76,0.12); color: var(--gold); }
  .status-frio    { background: var(--blue-dim); color: var(--blue); }
  .status-fechado { background: var(--teal-dim); color: var(--teal); }

  /* ─── FUNNEL ─────────────────────────────────── */
  .funnel { display: flex; flex-direction: column; gap: 10px; }
  .funnel-stage { position: relative; }
  .funnel-bar-wrap {
    height: 38px; background: var(--surface2);
    border-radius: 6px; overflow: hidden;
    margin-bottom: 4px; position: relative;
  }
  .funnel-bar {
    height: 100%;
    border-radius: 6px;
    display: flex; align-items: center; padding: 0 12px;
    transition: width 1.2s cubic-bezier(0.16,1,0.3,1);
  }
  .funnel-bar-label { font-size: 12px; font-weight: 500; color: rgba(0,0,0,0.7); white-space: nowrap; }
  .funnel-meta {
    display: flex; justify-content: space-between;
    font-size: 11px; color: var(--muted);
  }
  .funnel-count { color: var(--text); font-weight: 500; }

  /* ─── ACTIVITY ───────────────────────────────── */
  .activity-list { display: flex; flex-direction: column; gap: 0; }
  .activity-item {
    display: flex; gap: 12px;
    padding: 11px 0;
    border-bottom: 1px solid var(--border);
  }
  .activity-item:last-child { border-bottom: none; }
  .activity-dot {
    width: 7px; height: 7px; border-radius: 50%;
    margin-top: 6px; flex-shrink: 0;
  }
  .activity-body { flex: 1; }
  .activity-text { font-size: 12.5px; color: var(--text); line-height: 1.4; }
  .activity-time { font-size: 11px; color: var(--muted); margin-top: 2px; }

  /* ─── MAP PSEUDO ─────────────────────────────── */
  .map-wrap {
    position: relative;
    background: var(--surface2);
    border-radius: var(--r);
    overflow: hidden;
    height: 220px;
    margin-bottom: 16px;
  }
  .map-bg {
    width: 100%; height: 100%;
    display: block;
  }
  .map-pin {
    position: absolute;
    transform: translate(-50%, -100%);
    cursor: pointer;
  }
  .map-pin svg { display: block; filter: drop-shadow(0 2px 6px rgba(0,0,0,0.5)); }
  .map-pin .pin-label {
    position: absolute; bottom: calc(100% + 4px); left: 50%;
    transform: translateX(-50%);
    background: var(--surface);
    border: 1px solid var(--border2);
    border-radius: 5px;
    padding: 3px 7px;
    font-size: 10px; color: var(--text);
    white-space: nowrap;
    pointer-events: none;
    opacity: 0; transition: opacity 0.15s;
  }
  .map-pin:hover .pin-label { opacity: 1; }

  .map-legend {
    display: flex; gap: 14px; flex-wrap: wrap;
  }
  .legend-item { display: flex; align-items: center; gap: 5px; font-size: 11px; color: var(--muted2); }
  .legend-dot { width: 8px; height: 8px; border-radius: 50%; }

  /* ─── ANIMATIONS ─────────────────────────────── */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(18px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  /* stagger */
  .kpi-card:nth-child(1) { animation-delay: 0.10s; }
  .kpi-card:nth-child(2) { animation-delay: 0.18s; }
  .kpi-card:nth-child(3) { animation-delay: 0.26s; }
  .kpi-card:nth-child(4) { animation-delay: 0.34s; }
  .card:nth-child(1) { animation-delay: 0.42s; }
  .card:nth-child(2) { animation-delay: 0.50s; }
  .card:nth-child(3) { animation-delay: 0.54s; }
  .card:nth-child(4) { animation-delay: 0.58s; }
  .card:nth-child(5) { animation-delay: 0.62s; }

  /* ─── COUNTER ANIMATION ──────────────────────── */
  .counting { transition: all 0.05s; }

  /* ─── SCROLLBAR ──────────────────────────────── */
  ::-webkit-scrollbar { width: 5px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border2); border-radius: 99px; }

  /* ─── RESPONSIVE ─────────────────────────────── */
  @media (max-width: 1100px) {
    .kpi-grid { grid-template-columns: repeat(2,1fr); }
    .chart-grid { grid-template-columns: 1fr; }
    .bottom-grid { grid-template-columns: 1fr 1fr; }
  }
  @media (max-width: 720px) {
    .sidebar { display: none; }
    .main { padding: 24px 20px; }
    .bottom-grid { grid-template-columns: 1fr; }
    .kpi-grid { grid-template-columns: 1fr 1fr; }
  }

  /* ─── GITHUB BANNER ──────────────────────────── */
  .gh-banner {
    margin-top: 32px;
    background: linear-gradient(120deg, var(--surface) 60%, rgba(201,168,76,0.04) 100%);
    border: 1px solid var(--border);
    border-radius: var(--r-lg);
    padding: 20px 24px;
    display: flex; align-items: center; justify-content: space-between; gap: 16px;
    opacity: 0; animation: fadeUp 0.55s 0.9s forwards;
  }
  .gh-banner-left h3 { font-family: var(--font-serif); font-size: 16px; margin-bottom: 3px; }
  .gh-banner-left p { font-size: 12px; color: var(--muted2); }
  .gh-btn {
    display: inline-flex; align-items: center; gap: 7px;
    background: var(--gold); color: #0a0b0d;
    font-family: var(--font-sans); font-size: 12px; font-weight: 500;
    padding: 9px 18px; border-radius: 8px; text-decoration: none;
    transition: opacity 0.15s; white-space: nowrap;
    border: none; cursor: pointer;
  }
  .gh-btn:hover { opacity: 0.88; }
  .gh-btn-outline {
    display: inline-flex; align-items: center; gap: 7px;
    background: transparent; color: var(--text);
    border: 1px solid var(--border2);
    font-family: var(--font-sans); font-size: 12px;
    padding: 9px 18px; border-radius: 8px; text-decoration: none;
    transition: background 0.15s; white-space: nowrap; cursor: pointer;
  }
  .gh-btn-outline:hover { background: rgba(255,255,255,0.04); }
  .gh-btns { display: flex; gap: 8px; flex-shrink: 0; }

  .divider { border: none; border-top: 1px solid var(--border); margin: 28px 0; }

  .section-label {
    font-size: 10px; letter-spacing: 0.16em; text-transform: uppercase;
    color: var(--muted); margin-bottom: 16px;
  }
</style>
</head>
<body>

<div class="shell">

  <!-- SIDEBAR -->
  <aside class="sidebar">
    <div class="logo">
      <div class="logo-mark">Imob<br/>Dashboard</div>
      <div class="logo-sub">Marlos Gomes</div>
    </div>

    <nav class="nav">
      <div class="nav-section">Visão Geral</div>
      <div class="nav-item active"><span class="icon">◈</span> Painel</div>
      <div class="nav-item"><span class="icon">◉</span> Leads</div>
      <div class="nav-item"><span class="icon">▣</span> Imóveis</div>

      <div class="nav-section">Vendas</div>
      <div class="nav-item"><span class="icon">◈</span> Funil</div>
      <div class="nav-item"><span class="icon">◎</span> Metas</div>
      <div class="nav-item"><span class="icon">▦</span> Relatórios</div>

      <div class="nav-section">Configurações</div>
      <div class="nav-item"><span class="icon">◌</span> Perfil</div>
      <div class="nav-item"><span class="icon">◎</span> Integrações</div>
    </nav>

    <div class="sidebar-footer">
      <div class="avatar-row">
        <div class="avatar">M</div>
        <div class="avatar-info">
          <div class="name">Marlos Gomes</div>
          <div class="role">Corretor · DEV</div>
        </div>
      </div>
    </div>
  </aside>

  <!-- MAIN CONTENT -->
  <main class="main">

    <!-- TOPBAR -->
    <div class="topbar">
      <div>
        <div class="topbar-title">Bom dia, Marlos ✦</div>
        <div class="topbar-sub">Aqui está o resumo da sua carteira — Abril 2025</div>
      </div>
      <div class="period-tabs">
        <button class="period-tab" onclick="setPeriod('7d',this)">7d</button>
        <button class="period-tab active" onclick="setPeriod('30d',this)">30d</button>
        <button class="period-tab" onclick="setPeriod('90d',this)">90d</button>
        <button class="period-tab" onclick="setPeriod('12m',this)">12m</button>
      </div>
    </div>

    <!-- KPI CARDS -->
    <div class="kpi-grid">
      <div class="kpi-card gold">
        <div class="kpi-icon">💰</div>
        <div class="kpi-label">Volume de Vendas</div>
        <div class="kpi-value" id="kv">R$ 3,2M</div>
        <span class="kpi-delta up">▲ +18% vs mês ant.</span>
      </div>
      <div class="kpi-card teal">
        <div class="kpi-icon">🏠</div>
        <div class="kpi-label">Imóveis Fechados</div>
        <div class="kpi-value" id="kf">12</div>
        <span class="kpi-delta up">▲ +3 este mês</span>
      </div>
      <div class="kpi-card blue">
        <div class="kpi-icon">👥</div>
        <div class="kpi-label">Leads Ativos</div>
        <div class="kpi-value" id="kl">47</div>
        <span class="kpi-delta up">▲ +11 novos</span>
      </div>
      <div class="kpi-card coral">
        <div class="kpi-icon">📊</div>
        <div class="kpi-label">Taxa de Conversão</div>
        <div class="kpi-value" id="kt">24%</div>
        <span class="kpi-delta down">▼ -2% vs meta</span>
      </div>
    </div>

    <!-- CHARTS ROW -->
    <div class="chart-grid">

      <!-- Vendas chart -->
      <div class="card">
        <div class="card-header">
          <div class="card-title">Volume de Vendas Mensal</div>
          <span class="card-badge">R$ 3,2M este mês</span>
        </div>
        <canvas id="salesChart" height="110"></canvas>
      </div>

      <!-- Donut tipo imóvel -->
      <div class="card">
        <div class="card-header">
          <div class="card-title">Carteira por Tipo</div>
        </div>
        <canvas id="typeChart" height="160"></canvas>
        <div style="display:flex;flex-wrap:wrap;gap:10px;margin-top:16px">
          <div class="legend-item"><div class="legend-dot" style="background:#c9a84c"></div>Apartamento</div>
          <div class="legend-item"><div class="legend-dot" style="background:#2dd4a0"></div>Casa</div>
          <div class="legend-item"><div class="legend-dot" style="background:#5b9cf6"></div>Comercial</div>
          <div class="legend-item"><div class="legend-dot" style="background:#f4745a"></div>Terreno</div>
        </div>
      </div>
    </div>

    <!-- BOTTOM ROW -->
    <div class="bottom-grid">

      <!-- LEADS -->
      <div class="card">
        <div class="card-header">
          <div class="card-title">Leads em Aberto</div>
          <span style="font-size:11px;color:var(--muted)">47 total</span>
        </div>
        <div class="leads-list">
          <div class="lead-row">
            <div class="lead-dot" style="background:var(--coral)"></div>
            <div class="lead-info">
              <div class="lead-name">Carlos Henrique M.</div>
              <div class="lead-type">Apto 3 dorm · Mooca</div>
            </div>
            <div class="lead-value">R$680k</div>
            <span class="lead-status status-quente">Quente</span>
          </div>
          <div class="lead-row">
            <div class="lead-dot" style="background:var(--gold)"></div>
            <div class="lead-info">
              <div class="lead-name">Fernanda Oliveira</div>
              <div class="lead-type">Casa · Vila Prudente</div>
            </div>
            <div class="lead-value">R$490k</div>
            <span class="lead-status status-morno">Morno</span>
          </div>
          <div class="lead-row">
            <div class="lead-dot" style="background:var(--teal)"></div>
            <div class="lead-info">
              <div class="lead-name">Roberto Almeida</div>
              <div class="lead-type">Sala comercial · Centro</div>
            </div>
            <div class="lead-value">R$320k</div>
            <span class="lead-status status-fechado">Fechado ✓</span>
          </div>
          <div class="lead-row">
            <div class="lead-dot" style="background:var(--blue)"></div>
            <div class="lead-info">
              <div class="lead-name">Ana Paula Costa</div>
              <div class="lead-type">Apto 2 dorm · Tatuapé</div>
            </div>
            <div class="lead-value">R$410k</div>
            <span class="lead-status status-frio">Frio</span>
          </div>
          <div class="lead-row">
            <div class="lead-dot" style="background:var(--coral)"></div>
            <div class="lead-info">
              <div class="lead-name">Marcos Souza</div>
              <div class="lead-type">Terreno · Itaquera</div>
            </div>
            <div class="lead-value">R$210k</div>
            <span class="lead-status status-quente">Quente</span>
          </div>
        </div>
      </div>

      <!-- FUNNEL -->
      <div class="card">
        <div class="card-header">
          <div class="card-title">Funil de Vendas</div>
        </div>
        <div class="funnel">
          <div class="funnel-stage">
            <div class="funnel-bar-wrap">
              <div class="funnel-bar" id="f1" style="width:0%;background:var(--gold)">
                <span class="funnel-bar-label">Prospecção</span>
              </div>
            </div>
            <div class="funnel-meta"><span>Contatos iniciais</span><span class="funnel-count">84</span></div>
          </div>
          <div class="funnel-stage">
            <div class="funnel-bar-wrap">
              <div class="funnel-bar" id="f2" style="width:0%;background:var(--blue)">
                <span class="funnel-bar-label">Qualificados</span>
              </div>
            </div>
            <div class="funnel-meta"><span>Interesse confirmado</span><span class="funnel-count">47</span></div>
          </div>
          <div class="funnel-stage">
            <div class="funnel-bar-wrap">
              <div class="funnel-bar" id="f3" style="width:0%;background:var(--teal)">
                <span class="funnel-bar-label">Visitas</span>
              </div>
            </div>
            <div class="funnel-meta"><span>Imóvel visitado</span><span class="funnel-count">28</span></div>
          </div>
          <div class="funnel-stage">
            <div class="funnel-bar-wrap">
              <div class="funnel-bar" id="f4" style="width:0%;background:var(--coral)">
                <span class="funnel-bar-label">Proposta</span>
              </div>
            </div>
            <div class="funnel-meta"><span>Em negociação</span><span class="funnel-count">16</span></div>
          </div>
          <div class="funnel-stage">
            <div class="funnel-bar-wrap">
              <div class="funnel-bar" id="f5" style="width:0%;background:#8b5cf6">
                <span class="funnel-bar-label">Fechamento</span>
              </div>
            </div>
            <div class="funnel-meta"><span>Contratos assinados</span><span class="funnel-count">12</span></div>
          </div>
        </div>
      </div>

      <!-- ACTIVITY -->
      <div class="card">
        <div class="card-header">
          <div class="card-title">Atividade Recente</div>
        </div>
        <div class="activity-list">
          <div class="activity-item">
            <div class="activity-dot" style="background:var(--teal)"></div>
            <div class="activity-body">
              <div class="activity-text">Contrato assinado — Apto 3 dorm, Mooca · <strong>R$680.000</strong></div>
              <div class="activity-time">Hoje, 14h22</div>
            </div>
          </div>
          <div class="activity-item">
            <div class="activity-dot" style="background:var(--gold)"></div>
            <div class="activity-body">
              <div class="activity-text">Nova proposta enviada para Fernanda Oliveira — Casa Vila Prudente</div>
              <div class="activity-time">Hoje, 11h05</div>
            </div>
          </div>
          <div class="activity-item">
            <div class="activity-dot" style="background:var(--blue)"></div>
            <div class="activity-body">
              <div class="activity-text">Visita agendada — Ana Paula · Apto Tatuapé · Sáb 10h</div>
              <div class="activity-time">Ontem, 17h30</div>
            </div>
          </div>
          <div class="activity-item">
            <div class="activity-dot" style="background:var(--coral)"></div>
            <div class="activity-body">
              <div class="activity-text">Novo lead captado via Instagram — Marcos Souza</div>
              <div class="activity-time">Ontem, 09h14</div>
            </div>
          </div>
          <div class="activity-item">
            <div class="activity-dot" style="background:var(--muted)"></div>
            <div class="activity-body">
              <div class="activity-text">Meta mensal atingida 94% — 12 de 13 fechamentos</div>
              <div class="activity-time">26/04, 18h00</div>
            </div>
          </div>
        </div>
      </div>

    </div>

    <!-- MAP + METAS -->
    <div style="margin-top:28px">
      <div class="chart-grid">

        <!-- MAPA -->
        <div class="card">
          <div class="card-header">
            <div class="card-title">Mapa de Leads — São Paulo</div>
            <span class="card-badge">Zona Leste · Foco</span>
          </div>
          <div class="map-wrap">
            <svg class="map-bg" viewBox="0 0 520 220" xmlns="http://www.w3.org/2000/svg">
              <rect width="520" height="220" fill="#13161a"/>
              <!-- grid lines -->
              <g stroke="#1e2228" stroke-width="0.5">
                <line x1="0" y1="44" x2="520" y2="44"/>
                <line x1="0" y1="88" x2="520" y2="88"/>
                <line x1="0" y1="132" x2="520" y2="132"/>
                <line x1="0" y1="176" x2="520" y2="176"/>
                <line x1="104" y1="0" x2="104" y2="220"/>
                <line x1="208" y1="0" x2="208" y2="220"/>
                <line x1="312" y1="0" x2="312" y2="220"/>
                <line x1="416" y1="0" x2="416" y2="220"/>
              </g>
              <!-- SP map silhouette simplified blobs -->
              <path d="M40,160 Q80,140 120,130 Q160,120 200,115 Q240,110 270,105 Q300,100 330,95 Q360,90 390,100 Q420,110 440,130 Q460,150 450,175 Q440,200 400,205 Q360,210 320,208 Q280,206 240,200 Q200,194 160,188 Q120,182 80,185 Q50,188 40,175 Z" fill="#1a1e24" stroke="#252a32" stroke-width="1"/>
              <!-- roads -->
              <path d="M40,140 Q180,130 300,120 Q400,112 480,118" fill="none" stroke="#252a32" stroke-width="1.5"/>
              <path d="M200,60 Q210,100 215,140 Q218,170 220,200" fill="none" stroke="#252a32" stroke-width="1"/>
              <path d="M280,80 Q290,110 295,150 Q298,175 300,210" fill="none" stroke="#252a32" stroke-width="1"/>
              <!-- pins -->
              <!-- Mooca -->
              <g transform="translate(275,118)">
                <circle r="10" fill="rgba(201,168,76,0.15)" stroke="none"><animate attributeName="r" values="10;16;10" dur="2.5s" repeatCount="indefinite"/><animate attributeName="opacity" values="0.5;0;0.5" dur="2.5s" repeatCount="indefinite"/></circle>
                <circle r="5" fill="#c9a84c"/>
                <text x="8" y="-8" fill="#c9a84c" font-size="9" font-family="DM Sans,sans-serif">Mooca · R$680k</text>
              </g>
              <!-- Tatuapé -->
              <g transform="translate(330,100)">
                <circle r="4" fill="#5b9cf6"/>
                <text x="8" y="-6" fill="#5b9cf6" font-size="9" font-family="DM Sans,sans-serif">Tatuapé · R$410k</text>
              </g>
              <!-- Vila Prudente -->
              <g transform="translate(240,148)">
                <circle r="4" fill="#c9a84c"/>
                <text x="8" y="-6" fill="#c9a84c" font-size="9" font-family="DM Sans,sans-serif">V. Prudente · R$490k</text>
              </g>
              <!-- Itaquera -->
              <g transform="translate(390,120)">
                <circle r="10" fill="rgba(244,116,90,0.15)" stroke="none"><animate attributeName="r" values="10;16;10" dur="3s" repeatCount="indefinite"/><animate attributeName="opacity" values="0.5;0;0.5" dur="3s" repeatCount="indefinite"/></circle>
                <circle r="5" fill="#f4745a"/>
                <text x="8" y="-8" fill="#f4745a" font-size="9" font-family="DM Sans,sans-serif">Itaquera · R$210k</text>
              </g>
              <!-- Centro -->
              <g transform="translate(195,138)">
                <circle r="4" fill="#2dd4a0"/>
                <text x="8" y="-6" fill="#2dd4a0" font-size="9" font-family="DM Sans,sans-serif">Centro · R$320k</text>
              </g>
              <!-- label -->
              <text x="14" y="16" fill="#3a4050" font-size="9" font-family="DM Sans,sans-serif" letter-spacing="0.1em">SÃO PAULO — SP</text>
            </svg>
          </div>
          <div class="map-legend">
            <div class="legend-item"><div class="legend-dot" style="background:var(--coral)"></div>Lead quente (pulsando)</div>
            <div class="legend-item"><div class="legend-dot" style="background:var(--gold)"></div>Em negociação</div>
            <div class="legend-item"><div class="legend-dot" style="background:var(--teal)"></div>Fechado</div>
            <div class="legend-item"><div class="legend-dot" style="background:var(--blue)"></div>Frio</div>
          </div>
        </div>

        <!-- METAS -->
        <div class="card">
          <div class="card-header">
            <div class="card-title">Metas do Mês</div>
            <span style="font-size:11px;color:var(--muted)">Abril 2025</span>
          </div>

          <div style="display:flex;flex-direction:column;gap:18px">

            <div>
              <div style="display:flex;justify-content:space-between;font-size:12px;color:var(--muted2);margin-bottom:7px">
                <span>Volume de Vendas</span><span style="color:var(--gold)">R$3,2M / R$3,5M</span>
              </div>
              <div style="height:8px;background:var(--surface2);border-radius:99px;overflow:hidden">
                <div style="height:100%;width:91%;background:var(--gold);border-radius:99px;transition:width 1.2s cubic-bezier(0.16,1,0.3,1)"></div>
              </div>
              <div style="font-size:10px;color:var(--muted);margin-top:4px">91% da meta — faltam R$300k</div>
            </div>

            <div>
              <div style="display:flex;justify-content:space-between;font-size:12px;color:var(--muted2);margin-bottom:7px">
                <span>Imóveis Fechados</span><span style="color:var(--teal)">12 / 13</span>
              </div>
              <div style="height:8px;background:var(--surface2);border-radius:99px;overflow:hidden">
                <div style="height:100%;width:92%;background:var(--teal);border-radius:99px;"></div>
              </div>
              <div style="font-size:10px;color:var(--muted);margin-top:4px">92% — falta 1 fechamento</div>
            </div>

            <div>
              <div style="display:flex;justify-content:space-between;font-size:12px;color:var(--muted2);margin-bottom:7px">
                <span>Novos Leads</span><span style="color:var(--blue)">47 / 50</span>
              </div>
              <div style="height:8px;background:var(--surface2);border-radius:99px;overflow:hidden">
                <div style="height:100%;width:94%;background:var(--blue);border-radius:99px;"></div>
              </div>
              <div style="font-size:10px;color:var(--muted);margin-top:4px">94% — faltam 3 leads</div>
            </div>

            <div>
              <div style="display:flex;justify-content:space-between;font-size:12px;color:var(--muted2);margin-bottom:7px">
                <span>Taxa de Conversão</span><span style="color:var(--coral)">24% / 26%</span>
              </div>
              <div style="height:8px;background:var(--surface2);border-radius:99px;overflow:hidden">
                <div style="height:100%;width:76%;background:var(--coral);border-radius:99px;"></div>
              </div>
              <div style="font-size:10px;color:var(--muted);margin-top:4px">76% — 2pp abaixo da meta</div>
            </div>

            <div style="border-top:1px solid var(--border);padding-top:16px">
              <div style="display:flex;align-items:center;justify-content:space-between">
                <div>
                  <div style="font-size:11px;color:var(--muted);margin-bottom:2px">Score geral do mês</div>
                  <div style="font-family:var(--font-serif);font-size:28px;color:var(--gold)">91<span style="font-size:14px;color:var(--muted)">/100</span></div>
                </div>
                <div style="width:64px;height:64px">
                  <canvas id="scoreChart"></canvas>
                </div>
              </div>
            </div>
          </div>
        </div>

      </div>
    </div>

    <!-- GITHUB BANNER -->
    <div class="gh-banner">
      <div class="gh-banner-left">
        <h3>✦ Projeto Open Source</h3>
        <p>Dashboard construído com HTML, CSS e JS puro — sem frameworks. Ver código no GitHub.</p>
      </div>
      <div class="gh-btns">
        <a class="gh-btn-outline" href="https://github.com/DEVMarlosGomes" target="_blank">👤 Perfil</a>
        <a class="gh-btn" href="https://github.com/DEVMarlosGomes" target="_blank">⭐ Ver no GitHub</a>
      </div>
    </div>

  </main>
</div>

<script>
/* ─── CHART.JS GLOBAL CONFIG ──────────── */
Chart.defaults.color = '#6b6960';
Chart.defaults.font.family = "'DM Sans', sans-serif";
Chart.defaults.font.size = 11;

/* ─── SALES LINE CHART ────────────────── */
const salesCtx = document.getElementById('salesChart').getContext('2d');

const gradient = salesCtx.createLinearGradient(0, 0, 0, 200);
gradient.addColorStop(0, 'rgba(201,168,76,0.28)');
gradient.addColorStop(1, 'rgba(201,168,76,0)');

const salesData = {
  '7d':  { labels:['Seg','Ter','Qua','Qui','Sex','Sáb','Dom'], data:[120,0,340,0,680,210,0] },
  '30d': { labels:['Jan','Fev','Mar','Abr','Mai','Jun','Jul','Ago','Set','Out','Nov','Dez'], data:[1800,2100,1650,3200,2800,2400,1900,2600,3000,2200,2700,3100] },
  '90d': { labels:['Jan','Fev','Mar','Abr','Mai','Jun','Jul','Ago','Set','Out','Nov','Dez'], data:[1200,1800,2100,3200,2800,2400,2900,3300,2600,2200,3100,3500] },
  '12m': { labels:['2024 T1','2024 T2','2024 T3','2024 T4','2025 T1'], data:[5200,7800,8100,9400,9800] }
};

let salesChart = new Chart(salesCtx, {
  type: 'line',
  data: {
    labels: salesData['30d'].labels,
    datasets: [{
      data: salesData['30d'].data,
      borderColor: '#c9a84c',
      borderWidth: 2,
      fill: true,
      backgroundColor: gradient,
      tension: 0.45,
      pointRadius: 4,
      pointBackgroundColor: '#c9a84c',
      pointBorderColor: '#111316',
      pointBorderWidth: 2,
      pointHoverRadius: 6,
    }]
  },
  options: {
    responsive: true,
    plugins: {
      legend: { display: false },
      tooltip: {
        backgroundColor: '#181b1f',
        borderColor: 'rgba(255,255,255,0.1)',
        borderWidth: 1,
        titleColor: '#e8e6e0',
        bodyColor: '#c9a84c',
        callbacks: {
          label: ctx => ' R$ ' + (ctx.raw >= 1000 ? (ctx.raw/1000).toFixed(1)+'k' : ctx.raw) + 'k'
        }
      }
    },
    scales: {
      x: { grid: { color: 'rgba(255,255,255,0.04)' }, border: { display: false } },
      y: {
        grid: { color: 'rgba(255,255,255,0.04)' },
        border: { display: false },
        ticks: { callback: v => 'R$'+(v/1000).toFixed(0)+'k' }
      }
    }
  }
});

/* ─── DONUT CHART ─────────────────────── */
new Chart(document.getElementById('typeChart'), {
  type: 'doughnut',
  data: {
    labels: ['Apartamento','Casa','Comercial','Terreno'],
    datasets: [{
      data: [45, 28, 18, 9],
      backgroundColor: ['#c9a84c','#2dd4a0','#5b9cf6','#f4745a'],
      borderColor: '#111316',
      borderWidth: 3,
      hoverOffset: 6
    }]
  },
  options: {
    responsive: true,
    cutout: '68%',
    plugins: {
      legend: { display: false },
      tooltip: {
        backgroundColor: '#181b1f',
        borderColor: 'rgba(255,255,255,0.1)',
        borderWidth: 1,
        titleColor: '#e8e6e0',
        bodyColor: '#9a9690',
        callbacks: { label: ctx => ' ' + ctx.label + ': ' + ctx.raw + '%' }
      }
    }
  }
});

/* ─── SCORE GAUGE ─────────────────────── */
new Chart(document.getElementById('scoreChart'), {
  type: 'doughnut',
  data: {
    datasets: [{
      data: [91, 9],
      backgroundColor: ['#c9a84c', '#181b1f'],
      borderColor: 'transparent',
      borderWidth: 0,
    }]
  },
  options: {
    responsive: true,
    cutout: '72%',
    plugins: { legend: { display: false }, tooltip: { enabled: false } }
  }
});

/* ─── PERIOD SWITCH ───────────────────── */
function setPeriod(p, btn) {
  document.querySelectorAll('.period-tab').forEach(t => t.classList.remove('active'));
  btn.classList.add('active');
  const d = salesData[p];
  salesChart.data.labels = d.labels;
  salesChart.data.datasets[0].data = d.data;
  salesChart.update('active');
}

/* ─── FUNNEL ANIMATION ────────────────── */
setTimeout(() => {
  const targets = [
    { id:'f1', w:'100%' },
    { id:'f2', w:'56%' },
    { id:'f3', w:'33%' },
    { id:'f4', w:'19%' },
    { id:'f5', w:'14%' },
  ];
  targets.forEach(t => {
    document.getElementById(t.id).style.width = t.w;
  });
}, 600);

/* ─── NAV ITEMS ───────────────────────── */
document.querySelectorAll('.nav-item').forEach(item => {
  item.addEventListener('click', () => {
    document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
    item.classList.add('active');
  });
});

/* ─── GREETING BY TIME ────────────────── */
const h = new Date().getHours();
const greet = h < 12 ? 'Bom dia' : h < 18 ? 'Boa tarde' : 'Boa noite';
document.querySelector('.topbar-title').textContent = greet + ', Marlos ✦';
</script>
</body>
</html>
