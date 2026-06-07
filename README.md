<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Alertas de Obras Públicas Paralizadas</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <style>
    :root {
      --gfp-red: #d21e25;
      --gfp-blue: #152c53;
      --gfp-accent: #1c4574;
      --ink: #1f2130;
      --muted: #667085;
      --line: #d8dee8;
      --surface: #f4f6f9;
      --white: #ffffff;
      --amber: #f59e0b;
      --green: #1f9d63;
      --danger: #c62828;
      --sky: #2f80ed;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      color: var(--ink);
      font-family: Hind, "Segoe UI", Arial, sans-serif;
      background: var(--surface);
    }

    button, select, input {
      font: inherit;
    }

    .navbar {
      min-height: 76px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 20px;
      padding: 12px 24px 10px;
      background: var(--white);
      border-bottom: 1px solid var(--line);
      position: sticky;
      top: 0;
      z-index: 20;
    }

    .brand {
      display: flex;
      align-items: center;
      gap: 16px;
      min-width: 420px;
      color: var(--gfp-blue);
      font-weight: 800;
      font-size: 1.05rem;
    }

    .brand-logos {
      display: flex;
      align-items: center;
      gap: 14px;
    }

    .brand-logos a {
      display: inline-flex;
      align-items: center;
    }

    .brand-logo {
      display: block;
      max-width: 100%;
      object-fit: contain;
    }

    .brand-logo.gfp { width: 94px; height: 52px; }
    .brand-logo.seco { width: 112px; height: 50px; }
    .brand-logo.basel { width: 182px; height: 46px; }

    .brand-title {
      position: absolute;
      width: 1px;
      height: 1px;
      overflow: hidden;
      clip: rect(0 0 0 0);
    }

    .nav-tabs {
      display: flex;
      align-items: center;
      justify-content: flex-end;
      gap: 8px;
      margin: 0;
      padding: 0;
      list-style: none;
      flex-wrap: wrap;
    }

    .nav-tabs button {
      border: 1px solid transparent;
      background: transparent;
      color: var(--gfp-accent);
      padding: 8px 10px;
      border-radius: 6px;
      cursor: pointer;
      font-weight: 700;
      line-height: 1.1;
    }

    .nav-tabs button:hover,
    .nav-tabs button.active {
      color: var(--white);
      background: var(--gfp-accent);
    }

    .tab-panel {
      display: none;
    }

    .tab-panel.active {
      display: block;
    }

    .hero-red {
      background: var(--gfp-red);
      color: var(--white);
      padding: 12px 28px;
      font-size: 1.05rem;
      font-weight: 800;
      text-align: center;
    }

    .hero-red.empty {
      min-height: 34px;
      padding-top: 0;
      padding-bottom: 0;
    }

    .hero-blue {
      background: var(--gfp-blue);
      color: var(--white);
      padding: 26px 28px 30px;
      text-align: center;
    }

    .hero-blue h1,
    .hero-blue h2 {
      margin: 0 auto 8px;
      max-width: 1040px;
      font-size: clamp(1.7rem, 3vw, 2.55rem);
      line-height: 1.1;
    }

    .hero-blue p {
      max-width: 1080px;
      margin: 0 auto;
      color: #eef4ff;
      line-height: 1.55;
    }

    .layout-dashboard {
      display: grid;
      grid-template-columns: 1fr;
      gap: 18px;
      max-width: 1460px;
      margin: 0 auto;
      padding: 24px;
    }

    .well-panel,
    .panelbox,
    .value-box,
    .table-panel {
      background: var(--white);
      border: 1px solid var(--line);
      border-radius: 8px;
      box-shadow: 0 8px 22px rgba(21, 44, 83, 0.08);
    }

    .well-panel {
      padding: 16px;
      display: grid;
      grid-template-columns: repeat(6, minmax(140px, 1fr));
      gap: 12px;
      align-items: end;
    }

    .well-panel h3,
    .section-title {
      margin: 0 0 14px;
      color: var(--gfp-blue);
      font-size: 1.04rem;
      line-height: 1.2;
    }

    .well-panel h3 {
      grid-column: 1 / -1;
      margin-bottom: 2px;
    }

    .filter-field {
      min-width: 0;
    }

    label {
      display: block;
      margin: 0 0 6px;
      color: #334155;
      font-size: 0.88rem;
      font-weight: 700;
    }

    select,
    input[type="range"] {
      width: 100%;
    }

    select {
      min-height: 38px;
      border: 1px solid #cbd5e1;
      border-radius: 6px;
      background: #fff;
      color: #253044;
      padding: 7px 9px;
    }

    .btn {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      min-height: 38px;
      border: 0;
      border-radius: 6px;
      padding: 9px 12px;
      color: var(--white);
      background: var(--gfp-red);
      cursor: pointer;
      font-weight: 800;
    }

    .btn-block {
      width: 100%;
    }

    .btn.secondary {
      background: var(--gfp-accent);
    }

    .filter-status {
      display: none;
      align-items: center;
      justify-content: flex-start;
      gap: 12px;
      padding: 10px 12px;
      border: 1px solid #f0c66b;
      border-left: 5px solid var(--amber);
      border-radius: 8px;
      background: #fff8e8;
      color: #654405;
      box-shadow: 0 8px 22px rgba(21, 44, 83, 0.06);
    }

    .filter-status.active {
      display: flex;
      margin-bottom: 16px;
    }

    .filter-status-main {
      display: flex;
      align-items: center;
      flex-wrap: wrap;
      gap: 8px;
      min-width: 0;
    }

    .filter-status strong {
      color: #553900;
      white-space: nowrap;
    }

    .filter-chip {
      display: inline-flex;
      align-items: center;
      min-height: 26px;
      padding: 4px 8px;
      border-radius: 6px;
      background: #fff;
      border: 1px solid #f0d492;
      color: #6c4a00;
      font-size: 0.82rem;
      font-weight: 800;
    }

    .kpi-grid {
      display: grid;
      grid-template-columns: repeat(5, minmax(150px, 1fr));
      gap: 14px;
    }

    .value-box {
      min-height: 128px;
      padding: 16px;
      border-left: 5px solid var(--gfp-accent);
      position: relative;
      overflow: hidden;
    }

    .value-box .number {
      margin: 0;
      color: var(--gfp-blue);
      font-size: 2.05rem;
      line-height: 1;
      font-weight: 900;
      letter-spacing: 0;
      white-space: nowrap;
    }

    .value-box .number.compact {
      font-size: 1.84rem;
    }

    .value-box .label {
      margin: 9px 0 4px;
      color: #39455f;
      font-size: 0.78rem;
      line-height: 1.15;
      font-weight: 900;
      text-transform: uppercase;
    }

    .value-box .sub {
      display: block;
      color: var(--muted);
      font-size: 0.82rem;
      line-height: 1.2;
    }

    .value-box .source-note {
      margin-top: 3px;
      font-size: 0.74rem;
    }

    .dashboard-grid {
      display: grid;
      grid-template-columns: 1fr;
      gap: 18px;
      margin-top: 20px;
    }

    .panelbox,
    .table-panel {
      padding: 18px;
    }

    .map-wrap {
      min-height: 690px;
      display: block;
      align-items: stretch;
    }

    .peru-map {
      position: relative;
      min-height: 690px;
      border-radius: 8px;
      overflow: hidden;
      background: #d7e2e9;
      border: 1px solid #cbd5e1;
    }

    .leaflet-map {
      position: absolute;
      inset: 0;
      z-index: 1;
    }

    .map-toolbar {
      position: absolute;
      left: 16px;
      top: 16px;
      display: flex;
      gap: 7px;
      padding: 7px;
      border: 1px solid rgba(148, 163, 184, 0.55);
      border-radius: 8px;
      background: rgba(255,255,255,0.94);
      z-index: 500;
    }

    .map-layer-btn {
      min-height: 30px;
      border: 1px solid #cbd5e1;
      border-radius: 6px;
      padding: 5px 8px;
      color: var(--gfp-blue);
      background: #fff;
      cursor: pointer;
      font-size: 0.78rem;
      font-weight: 900;
    }

    .map-layer-btn.active {
      color: #fff;
      border-color: var(--gfp-accent);
      background: var(--gfp-accent);
    }

    .map-fullscreen-btn {
      position: absolute;
      top: 16px;
      right: 16px;
      min-height: 36px;
      border: 1px solid #cbd5e1;
      border-radius: 6px;
      padding: 7px 10px;
      color: var(--gfp-blue);
      background: rgba(255,255,255,0.96);
      box-shadow: 0 8px 20px rgba(21, 44, 83, 0.14);
      cursor: pointer;
      font-weight: 900;
      z-index: 500;
    }

    .map-reset-btn {
      position: absolute;
      right: 16px;
      top: 60px;
      min-height: 34px;
      border: 1px solid #cbd5e1;
      border-radius: 6px;
      padding: 7px 10px;
      color: var(--gfp-blue);
      background: rgba(255,255,255,0.96);
      box-shadow: 0 8px 20px rgba(21, 44, 83, 0.14);
      cursor: pointer;
      font-size: 0.82rem;
      font-weight: 900;
      z-index: 500;
    }

    .peru-map.fullscreen-map {
      position: fixed;
      inset: 14px;
      min-height: auto;
      border-radius: 8px;
      z-index: 2000;
      box-shadow: 0 28px 90px rgba(0,0,0,0.38);
    }

    .cost-marker {
      display: block;
      width: 22px;
      height: 22px;
      border: 3px solid #fff;
      border-radius: 999px;
      box-shadow: 0 0 0 5px rgba(21, 44, 83, 0.18), 0 8px 18px rgba(0,0,0,0.28);
    }

    .cost-marker.rango-50-plus { background: #b91c1c; }
    .cost-marker.rango-20-50 { background: #dc2626; }
    .cost-marker.rango-10-20 { background: #f97316; }
    .cost-marker.rango-5-10 { background: #f59e0b; }
    .cost-marker.rango-3-5 { background: #eab308; }
    .cost-marker.rango-1-3 { background: #22c55e; }
    .cost-marker.rango-05-1 { background: #0ea5e9; }
    .cost-marker.rango-05 { background: #2563eb; }

    .map-legend {
      position: absolute;
      left: 16px;
      top: 70px;
      background: rgba(255,255,255,0.94);
      border: 1px solid var(--line);
      border-radius: 8px;
      padding: 10px 12px;
      font-size: 0.84rem;
      z-index: 500;
    }

    .legend-row {
      display: flex;
      align-items: center;
      gap: 8px;
      margin: 5px 0;
      color: #344054;
    }

    .map-popup {
      position: absolute;
      right: 16px;
      top: 104px;
      width: min(320px, calc(100% - 32px));
      max-height: 360px;
      overflow: auto;
      display: none;
      padding: 14px;
      border: 1px solid #b8c4d6;
      border-top: 5px solid var(--gfp-red);
      border-radius: 8px;
      background: rgba(255, 255, 255, 0.97);
      box-shadow: 0 18px 34px rgba(21, 44, 83, 0.22);
      z-index: 500;
    }

    .map-popup.active {
      display: block;
    }

    .map-popup h4 {
      margin: 0 28px 6px 0;
      color: var(--gfp-blue);
      font-size: 1rem;
      line-height: 1.2;
    }

    .map-popup p {
      margin: 0 0 10px;
      color: var(--muted);
      font-size: 0.84rem;
      line-height: 1.35;
    }

    .map-popup-close {
      position: absolute;
      top: 8px;
      right: 8px;
      width: 26px;
      height: 26px;
      border: 1px solid #cbd5e1;
      border-radius: 6px;
      color: var(--gfp-blue);
      background: #fff;
      cursor: pointer;
      font-weight: 900;
      line-height: 1;
    }

    .popup-facts {
      display: grid;
      grid-template-columns: 1fr;
      gap: 7px;
    }

    .popup-fact {
      display: flex;
      justify-content: space-between;
      gap: 10px;
      padding: 8px;
      border-radius: 6px;
      background: #f6f8fb;
      color: #334155;
      font-size: 0.82rem;
      line-height: 1.2;
    }

    .popup-fact span {
      color: var(--muted);
      font-weight: 800;
    }

    .popup-fact strong {
      text-align: right;
      color: #24324a;
    }

    .dot {
      width: 10px;
      height: 10px;
      display: inline-block;
      border-radius: 999px;
    }

    .map-note {
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      bottom: 14px;
      width: min(940px, calc(100% - 190px));
      margin: 0 auto;
      padding: 7px 11px;
      border: 1px solid rgba(148, 163, 184, 0.72);
      border-radius: 8px;
      color: #24324a;
      background: rgba(255,255,255,0.94);
      box-shadow: 0 10px 22px rgba(21, 44, 83, 0.12);
      font-size: 0.78rem;
      line-height: 1.2;
      text-align: center;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      z-index: 500;
    }

    .chart-bars {
      display: grid;
      gap: 13px;
      margin-top: 4px;
    }

    .bar-item {
      display: grid;
      gap: 5px;
    }

    .bar-head {
      display: flex;
      justify-content: space-between;
      gap: 8px;
      color: #344054;
      font-size: 0.88rem;
      font-weight: 800;
    }

    .bar-track {
      height: 13px;
      border-radius: 999px;
      background: #edf2f7;
      overflow: hidden;
    }

    .bar-fill {
      height: 100%;
      border-radius: inherit;
      background: linear-gradient(90deg, var(--gfp-red), var(--amber));
    }

    .priority-table {
      width: 100%;
      border-collapse: collapse;
      overflow: hidden;
      font-size: 0.9rem;
    }

    .priority-table th,
    .priority-table td {
      padding: 11px 10px;
      border-bottom: 1px solid #e5eaf1;
      text-align: left;
      vertical-align: top;
    }

    .priority-table th {
      color: var(--gfp-blue);
      background: #f1f5fb;
      font-size: 0.78rem;
      text-transform: uppercase;
    }

    .score {
      display: inline-flex;
      min-width: 38px;
      min-height: 28px;
      align-items: center;
      justify-content: center;
      border-radius: 6px;
      color: #fff;
      background: var(--danger);
      font-weight: 900;
    }

    .downloads {
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 16px;
      max-width: 1180px;
      margin: 24px auto;
      padding: 0 24px;
    }

    .download-card {
      padding: 20px;
      border: 1px solid var(--line);
      border-radius: 8px;
      background: #fff;
    }

    .download-card h3 {
      margin: 0 0 8px;
      color: var(--gfp-blue);
    }

    .download-card p {
      color: var(--muted);
      min-height: 45px;
    }

    .footer-simple {
      margin-top: 22px;
      padding: 20px;
      text-align: center;
      color: var(--gfp-blue);
      background: #fff;
      border-top: 1px solid var(--line);
      font-weight: 700;
    }

    @media (max-width: 1180px) {
      .dashboard-grid,
      .map-wrap {
        grid-template-columns: 1fr;
      }

      .well-panel {
        grid-template-columns: repeat(3, minmax(160px, 1fr));
      }

      .kpi-grid {
        grid-template-columns: repeat(2, minmax(0, 1fr));
      }
    }

    @media (max-width: 760px) {
      .navbar {
        align-items: flex-start;
        flex-direction: column;
      }

      .brand {
        min-width: 0;
        width: 100%;
      }

      .brand-logos {
        flex-wrap: wrap;
        gap: 10px;
      }

      .brand-logo.gfp { width: 82px; height: 44px; }
      .brand-logo.seco { width: 96px; height: 42px; }
      .brand-logo.basel { width: 160px; height: 40px; }

      .map-wrap {
        min-height: auto;
      }

      .peru-map {
        min-height: 520px;
      }

      .well-panel {
        grid-template-columns: 1fr;
      }

      .layout-dashboard {
        padding: 16px;
      }

      .kpi-grid,
      .downloads {
        grid-template-columns: 1fr;
      }

      .hero-red,
      .hero-blue {
        padding-left: 16px;
        padding-right: 16px;
      }

      .filter-status {
        align-items: flex-start;
        flex-direction: column;
      }

      .map-toolbar {
        max-width: calc(100% - 32px);
        flex-wrap: wrap;
      }

      .map-legend {
        top: 112px;
      }

      .map-note {
        width: calc(100% - 32px);
      }

      .map-popup {
        right: 16px;
        top: 168px;
        max-height: 300px;
      }

    }
  </style>
</head>
<body>
  <nav class="navbar" aria-label="Navegación principal">
    <div class="brand">
      <div class="brand-logos" aria-label="GFP, SECO y Basel Institute on Governance">
        <a href="https://www.gfpsubnacional.pe/" target="_blank" rel="noopener noreferrer" aria-label="Abrir GFP Subnacional">
          <img class="brand-logo gfp" src="assets/GFP.png" alt="GFP Subnacional" />
        </a>
        <a href="https://www.cooperacionsuiza.pe/seco/" target="_blank" rel="noopener noreferrer" aria-label="Abrir SECO Cooperación Suiza">
          <img class="brand-logo seco" src="assets/SECO.png" alt="Cooperación Económica Suiza SECO" />
        </a>
        <a href="https://baselgovernance.org/" target="_blank" rel="noopener noreferrer" aria-label="Abrir Basel Institute on Governance">
          <img class="brand-logo basel" src="assets/BASEL.png" alt="Basel Institute on Governance" />
        </a>
      </div>
      <span class="brand-title">Alertas de Obras Públicas Paralizadas</span>
    </div>
    <ul class="nav-tabs">
      <li><button class="active" data-tab="dashboard" type="button">Dashboard</button></li>
      <li><button data-tab="priorizacion" type="button">Priorización</button></li>
      <li><button data-tab="reportes" type="button">Reportes</button></li>
    </ul>
  </nav>

  <section id="tab-dashboard" class="tab-panel active">
    <div class="hero-red empty" aria-hidden="true"></div>
    <div class="hero-blue">
      <h1>Alertas de Obras Públicas Paralizadas en el Perú</h1>
      <p>Plataforma basada en datos públicos para identificar y monitorear la paralización de obras en el Perú.</p>
    </div>

    <div class="layout-dashboard">
      <aside class="well-panel">
        <h3>Filtros de búsqueda</h3>
        <div class="filter-field">
          <label for="departamento">Departamento</label>
          <select id="departamento">
            <option>Todos</option>
            <option>Amazonas</option>
            <option>Áncash</option>
            <option>Apurímac</option>
            <option>Arequipa</option>
            <option>Ayacucho</option>
            <option>Cajamarca</option>
            <option>Callao</option>
            <option>Cusco</option>
            <option>Huancavelica</option>
            <option>Huánuco</option>
            <option>Ica</option>
            <option>Junín</option>
            <option>La Libertad</option>
            <option>Lambayeque</option>
            <option>Lima</option>
            <option>Loreto</option>
            <option>Madre de Dios</option>
            <option>Moquegua</option>
            <option>Pasco</option>
            <option>Piura</option>
            <option>Puno</option>
            <option>San Martín</option>
            <option>Tacna</option>
            <option>Tumbes</option>
            <option>Ucayali</option>
          </select>
        </div>

        <div class="filter-field">
          <label for="nivel">Nivel de gobierno</label>
          <select id="nivel">
            <option>Todos</option>
            <option>Gobierno nacional</option>
            <option>Gobierno regional</option>
            <option>Gobierno local</option>
          </select>
        </div>

        <div class="filter-field">
          <label for="sector">Sector</label>
          <select id="sector">
            <option>Todos</option>
            <option>Transporte</option>
            <option>Saneamiento</option>
            <option>Salud</option>
            <option>Educación</option>
            <option>Riego</option>
            <option>Otros</option>
          </select>
        </div>

        <div class="filter-field">
          <label for="causa">Causa principal</label>
          <select id="causa">
            <option>Todas</option>
            <option>Incumplimiento de contrato</option>
            <option>Desabastecimiento sostenido de materiales</option>
            <option>Otros</option>
            <option>Incumplimiento del pago de valorizaciones u otros</option>
            <option>Falta de recursos financieros y liquidez</option>
            <option>Deficiencia en el expediente técnico</option>
            <option>Discrepancias, controversias y arbitraje</option>
            <option>Conflictos sociales</option>
            <option>Abandono de obra</option>
            <option>Disponibilidad de terreno</option>
            <option>Falta de permisos, licencias y autorizaciones</option>
            <option>Eventos climáticos</option>
            <option>Interferencias</option>
            <option>Falta de personal clave</option>
            <option>Falta de supervisor o inspector</option>
            <option>Nulidad de oficio del contrato</option>
          </select>
        </div>

        <div class="filter-field">
          <label for="costo">Costo de la inversión</label>
          <select id="costo">
            <option value="todos">Todos</option>
            <option value="rango-50-plus">&gt; 50 millones</option>
            <option value="rango-20-50">&gt; 20 - 50 millones</option>
            <option value="rango-10-20">&gt; 10 - 20 millones</option>
            <option value="rango-5-10">&gt; 5 - 10 millones</option>
            <option value="rango-3-5">&gt; 3 - 5 millones</option>
            <option value="rango-1-3">&gt; 1 - 3 millones</option>
            <option value="rango-05-1">&gt; 0.5 - 1 millón</option>
            <option value="rango-05">&lt;= 0.5 millones</option>
          </select>
        </div>

        <button class="btn btn-block" id="btn-limpiar" type="button">Limpiar filtros</button>
      </aside>

      <main>
        <div class="filter-status" id="filter-status" aria-live="polite">
          <div class="filter-status-main">
            <strong>Vista filtrada</strong>
            <span id="filter-chips"></span>
          </div>
        </div>

        <h3 class="section-title">Indicadores clave</h3>
        <div class="kpi-grid">
          <div class="value-box" style="border-left-color: var(--danger)">
            <p class="number" id="kpi-obras">2,572</p>
            <p class="label">Obras paralizadas</p>
            <span class="sub" id="kpi-obras-sub">Corte referencial marzo 2025</span>
          </div>
          <div class="value-box" style="border-left-color: var(--gfp-red)">
            <p class="number compact" id="kpi-inversion">S/ 43.1 mil mill.</p>
            <p class="label">Inversión comprometida</p>
            <span class="sub" id="kpi-inversion-sub">Monto acumulado aproximado</span>
          </div>
          <div class="value-box" style="border-left-color: var(--amber)">
            <p class="number compact" id="kpi-saldo">S/ 22.5 mil mill.</p>
            <p class="label">Saldo por ejecutar</p>
            <span class="sub" id="kpi-saldo-sub">Recursos pendientes</span>
          </div>
          <div class="value-box" style="border-left-color: var(--sky)">
            <p class="number compact" id="kpi-personas">8.0 mill.</p>
            <p class="label">Población potencialmente afectada</p>
            <span class="sub source-note" id="kpi-personas-sub">Fuente: Sistema de Seguimiento de Inversiones</span>
          </div>
          <div class="value-box" style="border-left-color: var(--green)">
            <p class="number" id="kpi-regionales">327</p>
            <p class="label">Obras de nivel subnacional</p>
            <span class="sub" id="kpi-regionales-sub">según filtros activos</span>
          </div>
        </div>

        <div class="dashboard-grid">
          <section class="panelbox">
            <h3 class="section-title">Mapa de obras públicas paralizadas</h3>
            <div class="map-wrap">
              <div class="peru-map" aria-label="Mapa de obras públicas paralizadas">
                <div id="leaflet-map" class="leaflet-map"></div>
                <div class="map-toolbar" aria-label="Mapas base">
                  <button class="map-layer-btn active" data-base="satelital" type="button">Satelital</button>
                  <button class="map-layer-btn" data-base="topografico" type="button">Topográfico</button>
                  <button class="map-layer-btn" data-base="calles" type="button">Calles</button>
                </div>
                <button class="map-fullscreen-btn" id="map-fullscreen" type="button">Pantalla completa</button>
                <button class="map-reset-btn" id="map-reset" type="button">Restablecer vista</button>
                <div class="map-legend">
                  <strong>Obras por costo actualizado</strong>
                  <div class="legend-row"><span class="dot" style="background:#b91c1c"></span> &gt; 50 millones</div>
                  <div class="legend-row"><span class="dot" style="background:#dc2626"></span> &gt; 20 - 50 millones</div>
                  <div class="legend-row"><span class="dot" style="background:#f97316"></span> &gt; 10 - 20 millones</div>
                  <div class="legend-row"><span class="dot" style="background:#f59e0b"></span> &gt; 5 - 10 millones</div>
                  <div class="legend-row"><span class="dot" style="background:#eab308"></span> &gt; 3 - 5 millones</div>
                  <div class="legend-row"><span class="dot" style="background:#22c55e"></span> &gt; 1 - 3 millones</div>
                  <div class="legend-row"><span class="dot" style="background:#0ea5e9"></span> &gt; 0.5 - 1 millón</div>
                  <div class="legend-row"><span class="dot" style="background:#2563eb"></span> &lt;= 0.5 millones</div>
                </div>
                <aside class="map-popup" id="map-popup" aria-live="polite">
                  <button class="map-popup-close" id="map-popup-close" type="button" aria-label="Cerrar información">x</button>
                  <h4 id="popup-title">Selecciona una obra</h4>
                  <p id="popup-subtitle">Haz clic en un punto del mapa.</p>
                  <div class="popup-facts" id="popup-facts"></div>
                </aside>
                <p class="map-note" title="Ubicación de la obra es declarada por la entidad ejecutora (referencial). Fuente: INFOBRAS y GeoInvierte.">Ubicación de la obra es declarada por la entidad ejecutora (referencial). Fuente: INFOBRAS y GeoInvierte.</p>
              </div>
            </div>
          </section>

          <section class="panelbox">
            <h3 class="section-title">Causas de paralización</h3>
            <div class="chart-bars" id="causes-chart"></div>
          </section>
        </div>

      </main>
    </div>
    <footer class="footer-simple">GFP Subnacional | Alertas de Obras Públicas Paralizadas</footer>
  </section>

  <section id="tab-priorizacion" class="tab-panel">
    <div class="hero-red">Cartera ordenada por costo actualizado de inversión</div>
    <div class="hero-blue">
      <h2>Obras con mayor costo actualizado de inversión</h2>
      <p>La priorización muestra primero las obras paralizadas con mayor costo actualizado de inversión, para identificar dónde se concentra el mayor volumen de recursos públicos comprometidos.</p>
    </div>
    <div class="layout-dashboard" style="grid-template-columns:1fr">
      <section class="table-panel">
        <h3 class="section-title">Ranking de las 50 obras con mayor costo actualizado de inversión</h3>
        <table class="priority-table">
          <thead>
            <tr>
              <th>Orden</th>
              <th>Obra</th>
              <th>Región</th>
              <th>Sector</th>
              <th>Costo actualizado de inversión</th>
              <th>Saldo por ejecutar</th>
              <th>Causa</th>
            </tr>
          </thead>
          <tbody id="priority-table-body">
            <tr>
              <td><span class="score">1</span></td>
              <td>Hospital regional de alta complejidad</td>
              <td>Arequipa</td>
              <td>Salud</td>
              <td>S/ 620 mill.</td>
              <td>S/ 278 mill.</td>
              <td>Discrepancias, controversias y arbitraje</td>
            </tr>
            <tr>
              <td><span class="score" style="background:var(--amber)">2</span></td>
              <td>Carretera departamental eje productivo</td>
              <td>La Libertad</td>
              <td>Transporte</td>
              <td>S/ 310 mill.</td>
              <td>S/ 146 mill.</td>
              <td>Deficiencia en el expediente técnico</td>
            </tr>
            <tr>
              <td><span class="score" style="background:var(--sky)">3</span></td>
              <td>Sistema integral de agua potable</td>
              <td>Piura</td>
              <td>Saneamiento</td>
              <td>S/ 96 mill.</td>
              <td>S/ 38 mill.</td>
              <td>Falta de recursos financieros y liquidez</td>
            </tr>
            <tr>
              <td><span class="score" style="background:var(--sky)">4</span></td>
              <td>Institución educativa secundaria</td>
              <td>Cajamarca</td>
              <td>Educación</td>
              <td>S/ 42 mill.</td>
              <td>S/ 21 mill.</td>
              <td>Abandono de obra</td>
            </tr>
          </tbody>
        </table>
      </section>
    </div>
    <footer class="footer-simple">Priorización por costo actualizado de inversión</footer>
  </section>

  <section id="tab-reportes" class="tab-panel">
    <div class="hero-red">Productos para seguimiento y descarga</div>
    <div class="hero-blue">
      <h2>Reportes y bases de datos</h2>
      <p>Módulo para descargar fichas, bases depuradas y reportes por departamento, sector o entidad responsable.</p>
    </div>
    <div class="downloads">
      <article class="download-card">
        <h3>Base de datos</h3>
        <p>Listado de obras, ubicación, montos, causa y otros datos asociados.</p>
        <button class="btn secondary" type="button">Descargar base</button>
      </article>
      <article class="download-card">
        <h3>Documento de trabajo</h3>
        <p>Resumen ejecutivo con riesgos, avances, responsable y datos generales de la obra.</p>
        <button class="btn secondary" type="button">Descargar</button>
      </article>
    </div>
  </section>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script src="assets/obras-data.js"></script>
  <script>
    const tabs = document.querySelectorAll(".nav-tabs button");
    const panels = document.querySelectorAll(".tab-panel");
    const peruMap = document.querySelector(".peru-map");
    const mapPopup = document.getElementById("map-popup");
    const mapPopupClose = document.getElementById("map-popup-close");
    const fullscreenButton = document.getElementById("map-fullscreen");
    const resetMapButton = document.getElementById("map-reset");
    const filterControls = {
      departamento: document.getElementById("departamento"),
      nivel: document.getElementById("nivel"),
      sector: document.getElementById("sector"),
      causa: document.getElementById("causa"),
      costo: document.getElementById("costo")
    };
    const clearFiltersButton = document.getElementById("btn-limpiar");
    const filterStatus = document.getElementById("filter-status");
    const filterChips = document.getElementById("filter-chips");
    const causesChart = document.getElementById("causes-chart");
    const priorityTableBody = document.getElementById("priority-table-body");
    const kpiEls = {
      obras: document.getElementById("kpi-obras"),
      obrasSub: document.getElementById("kpi-obras-sub"),
      inversion: document.getElementById("kpi-inversion"),
      inversionSub: document.getElementById("kpi-inversion-sub"),
      saldo: document.getElementById("kpi-saldo"),
      saldoSub: document.getElementById("kpi-saldo-sub"),
      personas: document.getElementById("kpi-personas"),
      personasSub: document.getElementById("kpi-personas-sub"),
      regionales: document.getElementById("kpi-regionales"),
      regionalesSub: document.getElementById("kpi-regionales-sub")
    };
    let activePopupWork = null;
    const workData = window.OBRAS_PARALIZADAS_DATA || {};

    const leafletMap = L.map("leaflet-map", {
      zoomControl: false,
      preferCanvas: true,
      maxZoom: 17
    }).setView([-9.19, -75.02], 5);
    L.control.zoom({ position: "bottomright" }).addTo(leafletMap);

    const baseLayers = {
      satelital: L.tileLayer("https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}", {
        attribution: "Tiles Esri, Maxar, Earthstar Geographics",
        maxZoom: 17,
        maxNativeZoom: 17
      }),
      topografico: L.tileLayer("https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png", {
        attribution: "Map data OpenStreetMap, SRTM | OpenTopoMap",
        maxZoom: 15,
        maxNativeZoom: 15
      }),
      calles: L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution: "OpenStreetMap contributors",
        maxZoom: 19
      })
    };
    baseLayers.satelital.addTo(leafletMap);

    const maxZoomByBaseLayer = {
      satelital: 17,
      topografico: 15,
      calles: 19
    };

    function setMapBaseLayer(baseLayerKey) {
      Object.values(baseLayers).forEach((layer) => leafletMap.removeLayer(layer));
      baseLayers[baseLayerKey].addTo(leafletMap);
      leafletMap.setMaxZoom(maxZoomByBaseLayer[baseLayerKey]);
      if (leafletMap.getZoom() > maxZoomByBaseLayer[baseLayerKey]) {
        leafletMap.setZoom(maxZoomByBaseLayer[baseLayerKey]);
      }
    }

    leafletMap.createPane("department-boundaries");
    leafletMap.getPane("department-boundaries").style.zIndex = 450;
    leafletMap.getPane("department-boundaries").style.pointerEvents = "none";

    const departmentBoundaryStyle = {
      color: "#ffffff",
      weight: 1.2,
      opacity: 0.9,
      fillOpacity: 0,
      dashArray: "4 3",
      interactive: false
    };

    fetch("https://raw.githubusercontent.com/wmgeolab/geoBoundaries/main/releaseData/gbOpen/PER/ADM1/geoBoundaries-PER-ADM1_simplified.geojson")
      .then((response) => response.ok ? response.json() : Promise.reject(new Error("No se pudo cargar limites")))
      .then((geojson) => {
        L.geoJSON(geojson, {
          pane: "department-boundaries",
          style: departmentBoundaryStyle,
          interactive: false
        }).addTo(leafletMap);
      })
      .catch(() => {
        console.warn("No se pudo cargar la capa de limites departamentales.");
      });

    function getCostClass(cost) {
      if (cost > 50) return "rango-50-plus";
      if (cost > 20) return "rango-20-50";
      if (cost > 10) return "rango-10-20";
      if (cost > 5) return "rango-5-10";
      if (cost > 3) return "rango-3-5";
      if (cost > 1) return "rango-1-3";
      if (cost > 0.5) return "rango-05-1";
      return "rango-05";
    }

    function makeMarkerIcon(cost) {
      return L.divIcon({
        className: "",
        html: `<span class="cost-marker ${getCostMarkerClass(cost)}"></span>`,
        iconSize: [28, 28],
        iconAnchor: [14, 14]
      });
    }

    const markersByWork = Object.entries(workData).reduce((acc, [key, work]) => {
      const marker = L.marker(work.latlng, { icon: makeMarkerIcon(work.cost), title: work.title });
      marker.on("click", () => openMapPopup(key));
      marker.addTo(leafletMap);
      acc[key] = marker;
      return acc;
    }, {});

    const initialMapBounds = L.latLngBounds(Object.values(workData).map((work) => work.latlng));
    leafletMap.fitBounds(initialMapBounds, { padding: [36, 36] });

    tabs.forEach((tab) => {
      tab.addEventListener("click", () => {
        tabs.forEach((item) => item.classList.remove("active"));
        panels.forEach((panel) => panel.classList.remove("active"));
        tab.classList.add("active");
        document.getElementById(`tab-${tab.dataset.tab}`).classList.add("active");
        window.scrollTo({ top: 0, behavior: "smooth" });
      });
    });

    function matchesFilter(work) {
      const selectedRegion = filterControls.departamento.value;
      const selectedNivel = filterControls.nivel.value;
      const selectedSector = filterControls.sector.value;
      const selectedCausa = filterControls.causa.value;
      const selectedCosto = filterControls.costo.value;
      const costClass = getCostClass(work.cost);

      return (selectedRegion === "Todos" || work.region === selectedRegion)
        && (selectedNivel === "Todos" || work.nivel === selectedNivel)
        && (selectedSector === "Todos" || work.sector === selectedSector)
        && (selectedCausa === "Todas" || work.causa === selectedCausa)
        && (selectedCosto === "todos" || selectedCosto === costClass);
    }

    function getCostMarkerClass(cost) {
      return getCostClass(cost);
    }

    function getCostColor(cost) {
      const colorByCost = {
        "rango-50-plus": "#b91c1c",
        "rango-20-50": "#dc2626",
        "rango-10-20": "#f97316",
        "rango-5-10": "#f59e0b",
        "rango-3-5": "#eab308",
        "rango-1-3": "#22c55e",
        "rango-05-1": "#0ea5e9",
        "rango-05": "#2563eb"
      };
      return colorByCost[getCostClass(cost)];
    }

    function formatMoneyMillions(value) {
      if (value < 1) {
        return `S/ ${Math.round(value * 1000).toLocaleString("es-PE")} mil`;
      }
      const amount = value >= 1000 ? value / 1000 : value;
      const formatted = amount.toLocaleString("es-PE", {
        minimumFractionDigits: amount < 10 && amount % 1 !== 0 ? 1 : 0,
        maximumFractionDigits: amount < 10 ? 1 : 0
      });
      return value >= 1000 ? `S/ ${formatted} mil mill.` : `S/ ${formatted} mill.`;
    }

    function formatPeople(value) {
      if (value >= 1000000) return `${(value / 1000000).toFixed(1)} mill.`;
      if (value >= 1000) return `${Math.round(value / 1000).toLocaleString("es-PE")} mil`;
      return value.toLocaleString("es-PE");
    }

    function getActiveFilters() {
      const filters = [
        ["Departamento", filterControls.departamento.value, "Todos"],
        ["Nivel", filterControls.nivel.value, "Todos"],
        ["Sector", filterControls.sector.value, "Todos"],
        ["Causa", filterControls.causa.value, "Todas"],
        ["Costo", filterControls.costo.options[filterControls.costo.selectedIndex].text, "Todos"]
      ];
      return filters.filter(([, value, defaultValue]) => value !== defaultValue);
    }

    function updateFilterStatus() {
      const activeFilters = getActiveFilters();
      const hasFilters = activeFilters.length > 0;
      filterStatus.classList.toggle("active", hasFilters);
      clearFiltersButton.textContent = hasFilters ? "Limpiar filtros activos" : "Limpiar filtros";
      filterChips.innerHTML = activeFilters
        .map(([label, value]) => `<span class="filter-chip">${label}: ${value}</span>`)
        .join("");
    }

    function updateKpis(visibleWorks) {
      const investment = visibleWorks.reduce((sum, work) => sum + work.cost, 0);
      const saldo = visibleWorks.reduce((sum, work) => sum + work.saldo, 0);
      const peopleValues = visibleWorks
        .map((work) => work.people)
        .filter((value) => Number.isFinite(value));
      const people = peopleValues.reduce((sum, value) => sum + value, 0);
      const regional = visibleWorks.filter((work) => work.nivel === "Gobierno regional" || work.nivel === "Gobierno local").length;
      const scoped = getActiveFilters().length > 0 ? "según filtros activos" : "total de obras visibles";

      kpiEls.obras.textContent = visibleWorks.length.toLocaleString("es-PE");
      kpiEls.obrasSub.textContent = scoped;
      kpiEls.inversion.textContent = formatMoneyMillions(investment);
      kpiEls.inversionSub.textContent = "costo actualizado de inversión";
      kpiEls.saldo.textContent = formatMoneyMillions(saldo);
      kpiEls.saldoSub.textContent = "saldo por ejecutar";
      kpiEls.personas.textContent = peopleValues.length > 0 ? formatPeople(people) : "N/D";
      kpiEls.personasSub.textContent = "Fuente: Sistema de Seguimiento de Inversiones";
      kpiEls.regionales.textContent = regional.toLocaleString("es-PE");
      kpiEls.regionalesSub.textContent = "según filtros activos";
    }

    function resetMapView() {
      leafletMap.fitBounds(initialMapBounds, { padding: [36, 36] });
    }

    function updateCauseChart(visibleWorks) {
      if (visibleWorks.length === 0) {
        causesChart.innerHTML = `<div class="bar-item"><div class="bar-head"><span>Sin obras para los filtros seleccionados</span><span>0%</span></div><div class="bar-track"><div class="bar-fill" style="width:0%"></div></div></div>`;
        return;
      }

      const counts = visibleWorks.reduce((acc, work) => {
        acc[work.causa] = (acc[work.causa] || 0) + 1;
        return acc;
      }, {});

      causesChart.innerHTML = Object.entries(counts)
        .sort((a, b) => b[1] - a[1])
        .map(([cause, count]) => {
          const pctValue = (count / visibleWorks.length) * 100;
          const pctLabel = pctValue < 1 ? pctValue.toFixed(1) : Math.round(pctValue).toString();
          const barWidth = Math.max(pctValue, 1);
          return `<div class="bar-item"><div class="bar-head"><span>${cause}</span><span>${pctLabel}% - ${count.toLocaleString("es-PE")} obras</span></div><div class="bar-track"><div class="bar-fill" style="width:${barWidth}%"></div></div></div>`;
        })
        .join("");
    }

    function renderPriorityTable() {
      priorityTableBody.innerHTML = Object.values(workData)
        .sort((a, b) => b.cost - a.cost)
        .slice(0, 50)
        .map((work, index) => `<tr>
          <td><span class="score" style="background:${work.statusColor}">${index + 1}</span></td>
          <td>${work.title}</td>
          <td>${work.region}</td>
          <td>${work.sector}</td>
          <td>${formatMoneyMillions(work.cost)}</td>
          <td>${formatMoneyMillions(work.saldo)}</td>
          <td>${work.causa}</td>
        </tr>`)
        .join("");
    }

    function applyFilters() {
      const visibleWorkKeys = new Set();
      const visibleWorks = [];

      Object.entries(workData).forEach(([key, work]) => {
        const marker = markersByWork[key];
        const visible = matchesFilter(work);
        if (visible) {
          visibleWorkKeys.add(key);
          visibleWorks.push(work);
          if (!leafletMap.hasLayer(marker)) marker.addTo(leafletMap);
        } else if (leafletMap.hasLayer(marker)) {
          leafletMap.removeLayer(marker);
        }
      });

      if (activePopupWork && !visibleWorkKeys.has(activePopupWork)) {
        closeMapPopup();
      }

      const visibleLatLngs = Array.from(visibleWorkKeys).map((key) => workData[key].latlng);
      if (visibleLatLngs.length > 0) {
        leafletMap.fitBounds(visibleLatLngs, { padding: [48, 48], maxZoom: 7 });
      }

      updateFilterStatus();
      updateKpis(visibleWorks);
      updateCauseChart(visibleWorks);
    }

    function renderFacts(facts) {
      return Object.entries(facts)
        .map(([label, value]) => `<div class="popup-fact"><span>${label}</span><strong>${value}</strong></div>`)
        .join("");
    }

    function openMapPopup(workKey) {
      const work = workData[workKey];
      if (!work) return;

      document.getElementById("popup-title").textContent = work.title;
      document.getElementById("popup-subtitle").textContent = work.subtitle;
      document.getElementById("popup-facts").innerHTML = renderFacts(work.facts);
      mapPopup.style.borderTopColor = work.statusColor;
      mapPopup.classList.add("active");
      activePopupWork = workKey;
    }

    function closeMapPopup() {
      mapPopup.classList.remove("active");
      activePopupWork = null;
    }

    mapPopupClose.addEventListener("click", closeMapPopup);
    document.addEventListener("keydown", (event) => {
      if (event.key === "Escape") closeMapPopup();
    });

    Object.values(filterControls).forEach((control) => {
      control.addEventListener("change", applyFilters);
    });

    function resetFilters() {
      filterControls.departamento.value = "Todos";
      filterControls.nivel.value = "Todos";
      filterControls.sector.value = "Todos";
      filterControls.causa.value = "Todas";
      filterControls.costo.value = "todos";
      closeMapPopup();
      applyFilters();
    }

    clearFiltersButton.addEventListener("click", resetFilters);
    resetMapButton.addEventListener("click", resetMapView);

    document.querySelectorAll(".map-layer-btn").forEach((button) => {
      button.addEventListener("click", () => {
        document.querySelectorAll(".map-layer-btn").forEach((item) => item.classList.remove("active"));
        button.classList.add("active");
        setMapBaseLayer(button.dataset.base);
      });
    });

    fullscreenButton.addEventListener("click", () => {
      const isFullscreen = peruMap.classList.toggle("fullscreen-map");
      fullscreenButton.textContent = isFullscreen ? "Salir de pantalla completa" : "Pantalla completa";
      setTimeout(() => leafletMap.invalidateSize(), 180);
    });

    applyFilters();
    renderPriorityTable();
  </script>
</body>
</html>
