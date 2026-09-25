<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />

  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  />

  <title>HKDSE Microscope Simulation</title>

  <style>
    :root {
      --background: #edf5f8;
      --panel: #ffffff;
      --panel-soft: #f6fafc;
      --primary: #126c83;
      --primary-dark: #0a4658;
      --secondary: #47b8b0;
      --accent: #ffba4a;
      --success: #168558;
      --warning: #d57a13;
      --danger: #c84242;
      --text: #17313c;
      --muted: #627681;
      --border: #d5e1e7;
      --shadow: 0 12px 32px rgba(30, 64, 82, 0.12);
    }

    * {
      box-sizing: border-box;
    }

    html {
      scroll-behavior: smooth;
    }

    body {
      margin: 0;
      min-height: 100vh;
      color: var(--text);
      background:
        radial-gradient(circle at top left, #d7f1ef 0, transparent 36%),
        linear-gradient(135deg, #eaf3f7, #f9fbfc);
      font-family: Arial, Helvetica, sans-serif;
    }

    button,
    input {
      font: inherit;
    }

    button {
      cursor: pointer;
    }

    button:focus-visible,
    input:focus-visible,
    [tabindex]:focus-visible {
      outline: 3px solid rgba(18, 108, 131, 0.3);
      outline-offset: 3px;
    }

    .app-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 18px;
      padding: 18px 24px;
      color: white;
      background:
        linear-gradient(135deg, var(--primary-dark), var(--primary));
      box-shadow: 0 5px 20px rgba(8, 57, 73, 0.22);
    }

    .app-header h1 {
      margin: 0 0 5px;
      font-size: clamp(1.35rem, 2.8vw, 2rem);
    }

    .app-header p {
      margin: 0;
      color: #dff7fb;
      font-size: 0.95rem;
    }

    .header-actions {
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      justify-content: flex-end;
    }

    .button {
      min-height: 40px;
      padding: 9px 15px;
      border: 1px solid transparent;
      border-radius: 10px;
      color: white;
      background: var(--primary);
      font-weight: 700;
      transition:
        transform 0.15s ease,
        background 0.15s ease,
        box-shadow 0.15s ease;
    }

    .button:hover {
      transform: translateY(-1px);
      box-shadow: 0 5px 14px rgba(18, 108, 131, 0.2);
    }

    .button.secondary {
      color: var(--primary-dark);
      background: white;
      border-color: rgba(255, 255, 255, 0.5);
    }

    .button.danger {
      color: #fff;
      background: var(--danger);
    }

    .main-layout {
      width: min(1500px, calc(100% - 24px));
      margin: 18px auto 36px;
      display: grid;
      grid-template-columns: minmax(480px, 1.1fr) minmax(420px, 0.9fr);
      gap: 18px;
      align-items: start;
    }

    .panel {
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 18px;
      box-shadow: var(--shadow);
      overflow: hidden;
    }

    .panel-heading {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 14px;
      padding: 15px 18px;
      border-bottom: 1px solid var(--border);
      background: linear-gradient(180deg, #fff, #f7fbfc);
    }

    .panel-heading h2 {
      margin: 0;
      font-size: 1.08rem;
    }

    .badge {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      min-height: 29px;
      padding: 5px 10px;
      border-radius: 999px;
      color: white;
      background: var(--primary);
      font-size: 0.8rem;
      font-weight: 700;
      white-space: nowrap;
    }

    /* Microscope illustration */

    .microscope-area {
      position: relative;
      min-height: 650px;
      padding: 12px;
      background:
        radial-gradient(circle at 50% 35%, #ffffff 0, #eef7f9 55%, #e6f0f4);
    }

    #microscopeSvg {
      display: block;
      width: 100%;
      height: auto;
      max-height: 620px;
      margin: 0 auto;
    }

    .svg-label {
      fill: #17313c;
      font-size: 14px;
      font-weight: 700;
    }

    .svg-label-line {
      stroke: #52717f;
      stroke-width: 1.8;
      stroke-dasharray: 5 4;
    }

    .svg-part {
      transition: filter 0.2s ease;
    }

    .svg-part:hover {
      filter: brightness(1.06);
    }

    #nosepieceClickable {
      cursor: pointer;
    }

    #nosepieceGroup {
      transform-origin: 307px 189px;
      transition: transform 0.65s cubic-bezier(0.22, 0.9, 0.3, 1.15);
    }

    #stageGroup {
      transition: transform 0.2s ease;
    }

    #stageSlide {
      transition: transform 0.2s ease;
    }

    #lightBeam {
      transition:
        opacity 0.2s ease,
        fill 0.2s ease;
    }

    #coarseSvgKnob,
    #fineSvgKnob,
    #stageXSvgKnob,
    #stageYSvgKnob {
      transform-box: fill-box;
      transform-origin: center;
      transition: transform 0.1s linear;
    }

    .microscope-tip {
      margin: 0 15px 16px;
      padding: 10px 13px;
      border-left: 4px solid var(--primary);
      border-radius: 7px;
      color: #425c68;
      background: #eef9fb;
      font-size: 0.88rem;
      line-height: 1.45;
    }

    /* Right column */

    .right-column {
      display: grid;
      gap: 18px;
    }

    .viewer-panel {
      overflow: hidden;
    }

    .viewer-body {
      display: grid;
      grid-template-columns: minmax(260px, 1fr) 180px;
      gap: 14px;
      padding: 15px;
    }

    .eyepiece-container {
      position: relative;
      width: 100%;
      aspect-ratio: 1;
      overflow: hidden;
      border: 10px solid #20323a;
      border-radius: 50%;
      background: #05090b;
      box-shadow:
        inset 0 0 0 5px #4d626c,
        inset 0 0 40px #000,
        0 9px 22px rgba(10, 34, 43, 0.2);
    }

    #viewCanvas {
      display: block;
      width: 100%;
      height: 100%;
      background: #030708;
    }

    .crosshair {
      position: absolute;
      inset: 50% auto auto 50%;
      width: 22px;
      height: 22px;
      transform: translate(-50%, -50%);
      pointer-events: none;
      opacity: 0.28;
    }

    .crosshair::before,
    .crosshair::after {
      content: "";
      position: absolute;
      background: #17313c;
    }

    .crosshair::before {
      left: 10px;
      top: 0;
      width: 1px;
      height: 22px;
    }

    .crosshair::after {
      top: 10px;
      left: 0;
      width: 22px;
      height: 1px;
    }

    .viewer-message {
      position: absolute;
      left: 50%;
      bottom: 10%;
      width: max-content;
      max-width: 78%;
      transform: translateX(-50%);
      padding: 6px 11px;
      border-radius: 999px;
      color: white;
      background: rgba(7, 31, 39, 0.76);
      font-size: 0.79rem;
      font-weight: 700;
      text-align: center;
      pointer-events: none;
      backdrop-filter: blur(5px);
    }

    .viewer-data {
      display: grid;
      align-content: start;
      gap: 9px;
    }

    .data-card {
      padding: 10px;
      border: 1px solid var(--border);
      border-radius: 10px;
      background: var(--panel-soft);
    }

    .data-card span {
      display: block;
      margin-bottom: 3px;
      color: var(--muted);
      font-size: 0.75rem;
    }

    .data-card strong {
      font-size: 0.95rem;
    }

    .focus-meter {
      height: 12px;
      margin-top: 7px;
      overflow: hidden;
      border-radius: 999px;
      background: #dbe4e8;
    }

    #focusMeterFill {
      width: 0;
      height: 100%;
      border-radius: inherit;
      background: var(--danger);
      transition:
        width 0.2s ease,
        background 0.2s ease;
    }

    /* Instruction panel */

    .instruction-body {
      padding: 15px;
    }

    .current-instruction {
      padding: 14px;
      border: 1px solid #c6e2e7;
      border-left: 5px solid var(--primary);
      border-radius: 11px;
      background: #eff9fb;
    }

    .current-instruction h3 {
      margin: 0 0 7px;
      color: var(--primary-dark);
      font-size: 1rem;
    }

    .current-instruction p {
      margin: 0;
      line-height: 1.5;
      font-size: 0.91rem;
    }

    .progress-track {
      height: 10px;
      margin: 13px 0 15px;
      overflow: hidden;
      border-radius: 999px;
      background: #dde7eb;
    }

    #progressFill {
      width: 0%;
      height: 100%;
      border-radius: inherit;
      background: linear-gradient(90deg, var(--secondary), var(--success));
      transition: width 0.35s ease;
    }

    .steps {
      display: grid;
      gap: 7px;
      margin: 0;
      padding: 0;
      list-style: none;
    }

    .step {
      display: grid;
      grid-template-columns: 25px 1fr;
      gap: 9px;
      align-items: start;
      padding: 8px 9px;
      border: 1px solid transparent;
      border-radius: 9px;
      color: #5b6e77;
      background: #f7fafb;
      font-size: 0.84rem;
    }

    .step-number {
      display: grid;
      place-items: center;
      width: 24px;
      height: 24px;
      border-radius: 50%;
      color: white;
      background: #94a6ae;
      font-size: 0.76rem;
      font-weight: 700;
    }

    .step.active {
      color: var(--primary-dark);
      border-color: #abd7df;
      background: #edf9fb;
    }

    .step.active .step-number {
      background: var(--primary);
    }

    .step.complete {
      color: #256949;
      background: #eff9f4;
    }

    .step.complete .step-number {
      background: var(--success);
    }

    .feedback-box {
      margin-top: 12px;
      padding: 10px 12px;
      border-radius: 9px;
      color: #35505b;
      background: #edf2f4;
      font-size: 0.86rem;
      line-height: 1.4;
    }

    .feedback-box.success {
      color: #145c3b;
      background: #e4f6ec;
    }

    .feedback-box.warning {
      color: #774306;
      background: #fff1dc;
    }

    .feedback-box.danger {
      color: #7b2323;
      background: #fde8e8;
    }

    /* Controls */

    .controls-panel {
      grid-column: 1 / -1;
    }

    .controls-grid {
      display: grid;
      grid-template-columns: repeat(4, minmax(215px, 1fr));
      gap: 14px;
      padding: 16px;
    }

    .control-card {
      min-width: 0;
      padding: 14px;
      border: 1px solid var(--border);
      border-radius: 14px;
      background: linear-gradient(180deg, #fff, #f8fbfc);
    }

    .control-card h3 {
      margin: 0 0 4px;
      color: var(--primary-dark);
      font-size: 0.98rem;
    }

    .control-card > p {
      min-height: 35px;
      margin: 0 0 12px;
      color: var(--muted);
      font-size: 0.8rem;
      line-height: 1.4;
    }

    .objective-buttons {
      display: grid;
      gap: 7px;
    }

    .objective-button {
      display: flex;
      align-items: center;
      justify-content: space-between;
      min-height: 43px;
      padding: 8px 11px;
      border: 1px solid var(--border);
      border-radius: 9px;
      color: var(--text);
      background: white;
      font-weight: 700;
    }

    .objective-button:hover {
      border-color: var(--primary);
      background: #eff9fb;
    }

    .objective-button.active {
      color: white;
      border-color: var(--primary);
      background: var(--primary);
    }

    .objective-color {
      width: 12px;
      height: 22px;
      border: 2px solid rgba(0, 0, 0, 0.15);
      border-radius: 3px;
    }

    .objective-color.red {
      background: #d84b4b;
    }

    .objective-color.yellow {
      background: #f1c84b;
    }

    .objective-color.blue {
      background: #478fd0;
    }

    .dial-row {
      display: grid;
      grid-template-columns: 88px 1fr;
      align-items: center;
      gap: 10px;
    }

    .dial {
      position: relative;
      width: 82px;
      height: 82px;
      margin: auto;
      border: 7px solid #b7c4c9;
      border-radius: 50%;
      background:
        radial-gradient(circle at 35% 28%, #f9fbfc 0 7%, #78909b 9% 48%, #50656e 50% 100%);
      box-shadow:
        inset 0 0 0 3px #40535c,
        0 5px 11px rgba(33, 59, 69, 0.24);
      cursor: grab;
      touch-action: none;
      user-select: none;
    }

    .dial:active {
      cursor: grabbing;
    }

    .dial::after {
      content: "";
      position: absolute;
      left: 50%;
      top: 5px;
      width: 5px;
      height: 18px;
      transform: translateX(-50%);
      border-radius: 3px;
      background: #ffffff;
      box-shadow: 0 0 2px #15272e;
    }

    .dial.small {
      width: 65px;
      height: 65px;
      border-width: 6px;
    }

    .dial-controls {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 7px;
    }

    .dial-controls button {
      min-height: 36px;
      border: 1px solid var(--border);
      border-radius: 8px;
      color: var(--primary-dark);
      background: white;
      font-size: 1.1rem;
      font-weight: 800;
    }

    .dial-controls button:hover {
      color: white;
      border-color: var(--primary);
      background: var(--primary);
    }

    .dial-value {
      grid-column: 1 / -1;
      padding: 7px;
      border-radius: 7px;
      background: #eaf1f4;
      font-size: 0.8rem;
      font-weight: 700;
      text-align: center;
    }

    .dual-dials {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 9px;
    }

    .mini-dial-section {
      text-align: center;
    }

    .mini-dial-section h4 {
      margin: 0 0 7px;
      font-size: 0.82rem;
    }

    .mini-buttons {
      display: flex;
      justify-content: center;
      gap: 5px;
      margin-top: 7px;
    }

    .mini-buttons button {
      width: 33px;
      height: 31px;
      border: 1px solid var(--border);
      border-radius: 7px;
      color: var(--primary-dark);
      background: white;
      font-weight: 800;
    }

    .mini-buttons button:hover {
      color: white;
      background: var(--primary);
    }

    .light-control {
      display: grid;
      gap: 10px;
    }

    .light-control input[type="range"] {
      width: 100%;
      accent-color: var(--primary);
    }

    .light-output {
      display: flex;
      justify-content: space-between;
      gap: 10px;
      color: var(--muted);
      font-size: 0.79rem;
    }

    .power-button {
      width: 100%;
      min-height: 40px;
      border: 1px solid var(--border);
      border-radius: 9px;
      color: #445d67;
      background: #e6ecef;
      font-weight: 700;
    }

    .power-button.on {
      color: #67420c;
      border-color: #ebc268;
      background: #fff0bd;
      box-shadow: 0 0 14px rgba(255, 194, 62, 0.4);
    }

    .keyboard-help {
      padding: 0 16px 16px;
      color: var(--muted);
      font-size: 0.8rem;
    }

    .keyboard-help kbd {
      display: inline-block;
      min-width: 25px;
      margin: 2px;
      padding: 3px 6px;
      border: 1px solid #b7c5cb;
      border-bottom-width: 3px;
      border-radius: 5px;
      color: var(--text);
      background: white;
      font-family: inherit;
      font-weight: 700;
      text-align: center;
    }

    .modal {
      position: fixed;
      inset: 0;
      z-index: 100;
      display: none;
      place-items: center;
      padding: 20px;
      background: rgba(5, 26, 34, 0.65);
      backdrop-filter: blur(5px);
    }

    .modal.show {
      display: grid;
    }

    .modal-card {
      width: min(520px, 100%);
      padding: 25px;
      border-radius: 18px;
      background: white;
      box-shadow: 0 20px 55px rgba(0, 0, 0, 0.28);
      text-align: center;
    }

    .modal-icon {
      display: grid;
      place-items: center;
      width: 70px;
      height: 70px;
      margin: 0 auto 15px;
      border-radius: 50%;
      color: white;
      background: var(--success);
      font-size: 2rem;
    }

    .modal-card h2 {
      margin: 0 0 8px;
      color: var(--primary-dark);
    }

    .modal-card p {
      color: #536b75;
      line-height: 1.5;
    }

    .score {
      margin: 15px 0;
      color: var(--primary);
      font-size: 1.4rem;
      font-weight: 800;
    }

    @media (max-width: 1120px) {
      .main-layout {
        grid-template-columns: 1fr;
      }

      .controls-panel {
        grid-column: auto;
      }

      .controls-grid {
        grid-template-columns: repeat(2, 1fr);
      }

      .microscope-area {
        min-height: auto;
      }
    }

    @media (max-width: 650px) {
      .app-header {
        align-items: flex-start;
        flex-direction: column;
      }

      .main-layout {
        width: min(100% - 12px, 1500px);
        margin-top: 8px;
      }

      .viewer-body {
        grid-template-columns: 1fr;
      }

      .viewer-data {
        grid-template-columns: repeat(2, 1fr);
      }

      .controls-grid {
        grid-template-columns: 1fr;
      }

      .control-card > p {
        min-height: 0;
      }

      .microscope-area {
        padding: 3px;
      }

      .svg-label {
        font-size: 12px;
      }
    }
  </style>
</head>

<body>
  <header class="app-header">
    <div>
      <h1>HKDSE Microscope Skills Simulation</h1>
      <p>
        Practise stage adjustment, focusing, illumination and changing objectives.
      </p>
    </div>

    <div class="header-actions">
      <button id="toggleLabelsButton" class="button secondary" type="button">
        Hide labels
      </button>

      <button id="resetButton" class="button danger" type="button">
        Reset simulation
      </button>
    </div>
  </header>

  <main class="main-layout">
    <!-- Microscope diagram -->
    <section class="panel">
      <div class="panel-heading">
        <h2>Compound Light Microscope</h2>
        <span class="badge" id="objectiveBadge">Low power: 4×</span>
      </div>

      <div class="microscope-area">
        <svg
          id="microscopeSvg"
          viewBox="0 0 620 650"
          role="img"
          aria-label="Interactive labelled diagram of a compound light microscope"
        >
          <defs>
            <linearGradient id="metalGradient" x1="0" y1="0" x2="1" y2="1">
              <stop offset="0%" stop-color="#f6f8f9" />
              <stop offset="45%" stop-color="#aabac1" />
              <stop offset="100%" stop-color="#657b85" />
            </linearGradient>

            <linearGradient id="darkMetal" x1="0" y1="0" x2="1" y2="1">
              <stop offset="0%" stop-color="#6f858e" />
              <stop offset="100%" stop-color="#2c414a" />
            </linearGradient>

            <radialGradient id="lampGlow">
              <stop offset="0%" stop-color="#fff9bf" stop-opacity="1" />
              <stop offset="50%" stop-color="#ffd95f" stop-opacity="0.7" />
              <stop offset="100%" stop-color="#ffd95f" stop-opacity="0" />
            </radialGradient>

            <filter id="shadow">
              <feDropShadow
                dx="0"
                dy="5"
                stdDeviation="5"
                flood-color="#1b3742"
                flood-opacity="0.25"
              />
            </filter>
          </defs>

          <!-- Base -->
          <path
            class="svg-part"
            d="M150 568 Q145 541 179 531 L426 531 Q461 541 466 568
               L492 600 Q497 618 469 622 L128 622 Q100 618 107 599 Z"
            fill="url(#darkMetal)"
            stroke="#253e48"
            stroke-width="5"
            filter="url(#shadow)"
          />

          <path
            d="M137 581 Q260 564 456 581 L468 602 L122 602 Z"
            fill="#d7e1e5"
            opacity="0.36"
          />

          <!-- Arm -->
          <path
            class="svg-part"
            d="M396 543
               C475 486 479 373 443 285
               C421 232 391 194 358 165
               L319 203
               C354 235 382 270 397 313
               C426 395 409 464 350 508 Z"
            fill="url(#metalGradient)"
            stroke="#334e59"
            stroke-width="7"
            filter="url(#shadow)"
          />

          <!-- Eyepiece -->
          <g class="svg-part">
            <path
              d="M238 54 L311 54 L325 79 L224 79 Z"
              fill="#233840"
              stroke="#13262d"
              stroke-width="4"
            />

            <rect
              x="240"
              y="76"
              width="68"
              height="61"
              rx="11"
              fill="url(#darkMetal)"
              stroke="#253d47"
              stroke-width="5"
            />

            <path
              d="M247 132 L303 132 L347 173 L310 207 L254 160 Z"
              fill="url(#metalGradient)"
              stroke="#334e59"
              stroke-width="6"
            />
          </g>

          <!-- Nosepiece and objectives -->
          <g
            id="nosepieceClickable"
            role="button"
            tabindex="0"
            aria-label="Rotate objective nosepiece"
          >
            <g id="nosepieceGroup">
              <ellipse
                cx="307"
                cy="189"
                rx="66"
                ry="29"
                fill="#354b54"
                stroke="#1c3038"
                stroke-width="5"
              />

              <!-- Low objective -->
              <g>
                <rect
                  x="270"
                  y="203"
                  width="27"
                  height="72"
                  rx="6"
                  fill="url(#metalGradient)"
                  stroke="#354d57"
                  stroke-width="4"
                />
                <rect
                  x="270"
                  y="237"
                  width="27"
                  height="13"
                  fill="#d84b4b"
                />
                <text
                  x="283"
                  y="229"
                  text-anchor="middle"
                  fill="#20343c"
                  font-size="11"
                  font-weight="bold"
                >4×</text>
              </g>

              <!-- Medium objective -->
              <g transform="rotate(-58 307 189)">
                <rect
                  x="294"
                  y="203"
                  width="27"
                  height="83"
                  rx="6"
                  fill="url(#metalGradient)"
                  stroke="#354d57"
                  stroke-width="4"
                />
                <rect
                  x="294"
                  y="244"
                  width="27"
                  height="13"
                  fill="#f1c84b"
                />
                <text
                  x="307"
                  y="234"
                  text-anchor="middle"
                  fill="#20343c"
                  font-size="10"
                  font-weight="bold"
                >10×</text>
              </g>

              <!-- High objective -->
              <g transform="rotate(58 307 189)">
                <rect
                  x="294"
                  y="203"
                  width="27"
                  height="96"
                  rx="6"
                  fill="url(#metalGradient)"
                  stroke="#354d57"
                  stroke-width="4"
                />
                <rect
                  x="294"
                  y="253"
                  width="27"
                  height="13"
                  fill="#478fd0"
                />
                <text
                  x="307"
                  y="241"
                  text-anchor="middle"
                  fill="#20343c"
                  font-size="10"
                  font-weight="bold"
                >40×</text>
              </g>
            </g>
          </g>

          <!-- Stage and slide -->
          <g id="stageGroup">
            <rect
              x="173"
              y="327"
              width="272"
              height="29"
              rx="7"
              fill="#314850"
              stroke="#172d35"
              stroke-width="5"
              filter="url(#shadow)"
            />

            <rect
              x="200"
              y="302"
              width="215"
              height="30"
              rx="5"
              fill="#82959d"
              stroke="#344e58"
              stroke-width="4"
            />

            <g id="stageSlide">
              <rect
                x="244"
                y="308"
                width="128"
                height="15"
                rx="3"
                fill="#bde9ee"
                stroke="#43818e"
                stroke-width="2"
                opacity="0.92"
              />

              <ellipse
                cx="308"
                cy="316"
                rx="19"
                ry="6"
                fill="#cf8bc7"
                opacity="0.8"
              />

              <path
                d="M246 305 L258 298 L258 330 L246 324 Z"
                fill="#556c76"
              />

              <path
                d="M370 305 L358 298 L358 330 L370 324 Z"
                fill="#556c76"
              />
            </g>

            <circle
              cx="309"
              cy="341"
              r="17"
              fill="#142a32"
            />
          </g>

          <!-- Condenser -->
          <g class="svg-part">
            <path
              d="M275 374 Q309 357 343 374 L333 410 L285 410 Z"
              fill="url(#metalGradient)"
              stroke="#344e58"
              stroke-width="4"
            />

            <rect
              x="278"
              y="410"
              width="62"
              height="10"
              rx="5"
              fill="#273d46"
            />
          </g>

          <!-- Light beam -->
          <path
            id="lightBeam"
            d="M273 526 L297 417 L321 417 L347 526 Z"
            fill="#ffe777"
            opacity="0"
          />

          <!-- Illuminator -->
          <g class="svg-part">
            <ellipse
              id="lampGlow"
              cx="310"
              cy="512"
              rx="76"
              ry="60"
              fill="url(#lampGlow)"
              opacity="0"
            />

            <path
              d="M263 522 Q309 479 355 522 L347 550 L272 550 Z"
              fill="#506872"
              stroke="#243b44"
              stroke-width="5"
            />

            <ellipse
              cx="309"
              cy="520"
              rx="35"
              ry="13"
              fill="#d3e0e4"
              stroke="#314a54"
              stroke-width="4"
            />

            <ellipse
              id="lampSurface"
              cx="309"
              cy="520"
              rx="25"
              ry="8"
              fill="#4d5d63"
            />
          </g>

          <!-- Focus knobs -->
          <g class="svg-part">
            <circle
              id="coarseSvgKnob"
              cx="432"
              cy="296"
              r="38"
              fill="#4b626c"
              stroke="#253b44"
              stroke-width="6"
            />

            <circle
              cx="432"
              cy="296"
              r="17"
              fill="#7f959e"
              stroke="#253b44"
              stroke-width="4"
            />

            <line
              x1="432"
              y1="260"
              x2="432"
              y2="273"
              stroke="white"
              stroke-width="5"
              stroke-linecap="round"
            />

            <circle
              id="fineSvgKnob"
              cx="432"
              cy="296"
              r="14"
              fill="#b2c0c6"
              stroke="#314851"
              stroke-width="3"
            />

            <line
              x1="432"
              y1="283"
              x2="432"
              y2="289"
              stroke="#17313c"
              stroke-width="3"
              stroke-linecap="round"
            />
          </g>

          <!-- Stage knobs -->
          <g class="svg-part">
            <circle
              id="stageXSvgKnob"
              cx="458"
              cy="365"
              r="15"
              fill="#617882"
              stroke="#253b44"
              stroke-width="4"
            />

            <line
              x1="458"
              y1="352"
              x2="458"
              y2="358"
              stroke="white"
              stroke-width="3"
            />

            <circle
              id="stageYSvgKnob"
              cx="478"
              cy="389"
              r="13"
              fill="#617882"
              stroke="#253b44"
              stroke-width="4"
            />

            <line
              x1="478"
              y1="378"
              x2="478"
              y2="383"
              stroke="white"
              stroke-width="3"
            />
          </g>

          <!-- Labels -->
          <g id="microscopeLabels">
            <line class="svg-label-line" x1="155" y1="63" x2="235" y2="65" />
            <text class="svg-label" x="58" y="67">Eyepiece lens</text>

            <line class="svg-label-line" x1="113" y1="150" x2="259" y2="149" />
            <text class="svg-label" x="30" y="154">Body tube</text>

            <line class="svg-label-line" x1="106" y1="209" x2="252" y2="194" />
            <text class="svg-label" x="25" y="213">Nosepiece</text>

            <line class="svg-label-line" x1="100" y1="273" x2="277" y2="250" />
            <text class="svg-label" x="16" y="277">Objective</text>

            <line class="svg-label-line" x1="91" y1="333" x2="174" y2="338" />
            <text class="svg-label" x="30" y="337">Stage</text>

            <line class="svg-label-line" x1="84" y1="393" x2="278" y2="391" />
            <text class="svg-label" x="12" y="397">Condenser</text>

            <line class="svg-label-line" x1="79" y1="519" x2="270" y2="523" />
            <text class="svg-label" x="11" y="523">Light source</text>

            <line class="svg-label-line" x1="486" y1="255" x2="450" y2="282" />
            <text class="svg-label" x="488" y="250">Coarse adjustment</text>

            <line class="svg-label-line" x1="491" y1="306" x2="448" y2="299" />
            <text class="svg-label" x="493" y="310">Fine adjustment</text>

            <line class="svg-label-line" x1="510" y1="366" x2="471" y2="369" />
            <text class="svg-label" x="512" y="370">Stage controls</text>

            <line class="svg-label-line" x1="492" y1="445" x2="421" y2="421" />
            <text class="svg-label" x="494" y="449">Arm</text>

            <line class="svg-label-line" x1="484" y1="586" x2="440" y2="586" />
            <text class="svg-label" x="486" y="590">Base</text>
          </g>
        </svg>

        <p class="microscope-tip">
          <strong>Interactive microscope:</strong>
          click the nosepiece to rotate it, or use the objective buttons below.
          Drag or scroll the control knobs to adjust the microscope.
        </p>
      </div>
    </section>

    <!-- Viewer and guide -->
    <div class="right-column">
      <section class="panel viewer-panel">
        <div class="panel-heading">
          <h2>View through the Eyepiece</h2>
          <span class="badge" id="totalMagnificationBadge">
            Total magnification: 40×
          </span>
        </div>

        <div class="viewer-body">
          <div class="eyepiece-container">
            <canvas
              id="viewCanvas"
              width="600"
              height="600"
              aria-label="Microscope image of onion epidermal cells"
            ></canvas>

            <div class="crosshair" aria-hidden="true"></div>

            <div id="viewerMessage" class="viewer-message">
              Switch on the light.
            </div>
          </div>

          <div class="viewer-data">
            <div class="data-card">
              <span>Objective</span>
              <strong id="objectiveReadout">4× low power</strong>
            </div>

            <div class="data-card">
              <span>Stage X / Y</span>
              <strong id="stageReadout">+30 / −24</strong>
            </div>

            <div class="data-card">
              <span>Focus position</span>
              <strong id="focusReadout">−14.0</strong>

              <div class="focus-meter" title="Image sharpness">
                <div id="focusMeterFill"></div>
              </div>
            </div>

            <div class="data-card">
              <span>Light intensity</span>
              <strong id="lightReadout">0%</strong>
            </div>
          </div>
        </div>
      </section>

      <section class="panel">
        <div class="panel-heading">
          <h2>Guided Procedure</h2>
          <span class="badge" id="scoreBadge">Score: 100</span>
        </div>

        <div class="instruction-body">
          <div class="current-instruction">
            <h3 id="instructionTitle">Step 1: Switch on the light</h3>
            <p id="instructionText">
              Switch on the illuminator and set the light to a moderate
              intensity.
            </p>
          </div>

          <div class="progress-track">
            <div id="progressFill"></div>
          </div>

          <ol class="steps" id="stepList">
            <li class="step active">
              <span class="step-number">1</span>
              <span>Switch on the light and set a moderate intensity.</span>
            </li>

            <li class="step">
              <span class="step-number">2</span>
              <span>Begin with the low-power 4× objective.</span>
            </li>

            <li class="step">
              <span class="step-number">3</span>
              <span>Use the stage controls to centre the specimen.</span>
            </li>

            <li class="step">
              <span class="step-number">4</span>
              <span>Use coarse adjustment to obtain approximate focus.</span>
            </li>

            <li class="step">
              <span class="step-number">5</span>
              <span>Use fine adjustment to obtain a sharp image.</span>
            </li>

            <li class="step">
              <span class="step-number">6</span>
              <span>Rotate the nosepiece to the high-power objective.</span>
            </li>

            <li class="step">
              <span class="step-number">7</span>
              <span>Re-centre the specimen under high power if necessary.</span>
            </li>

            <li class="step">
              <span class="step-number">8</span>
              <span>Use only fine adjustment for high-power focus.</span>
            </li>
          </ol>

          <div id="feedbackBox" class="feedback-box" aria-live="polite">
            Follow the procedure in order. Begin by switching on the light.
          </div>
        </div>
      </section>
    </div>

    <!-- Controls -->
    <section class="panel controls-panel">
      <div class="panel-heading">
        <h2>Microscope Controls</h2>
        <span class="badge">Drag, scroll or use + / −</span>
      </div>

      <div class="controls-grid">
        <!-- Objective controls -->
        <article class="control-card">
          <h3>1. Revolving Nosepiece</h3>
          <p>
            Rotate the nosepiece to place an objective over the specimen.
          </p>

          <div class="objective-buttons">
            <button
              class="objective-button active"
              data-objective="0"
              type="button"
            >
              <span>Low power — 4×</span>
              <span class="objective-color red"></span>
            </button>

            <button
              class="objective-button"
              data-objective="1"
              type="button"
            >
              <span>Medium power — 10×</span>
              <span class="objective-color yellow"></span>
            </button>

            <button
              class="objective-button"
              data-objective="2"
              type="button"
            >
              <span>High power — 40×</span>
              <span class="objective-color blue"></span>
            </button>
          </div>
        </article>

        <!-- Stage controls -->
        <article class="control-card">
          <h3>2. Mechanical Stage</h3>
          <p>
            Move the slide horizontally. The image appears to move in the
            opposite direction.
          </p>

          <div class="dual-dials">
            <div class="mini-dial-section">
              <h4>X position</h4>

              <div
                id="stageXDial"
                class="dial small"
                tabindex="0"
                role="slider"
                aria-label="Stage X position"
              ></div>

              <div class="mini-buttons">
                <button type="button" data-action="stage-x-minus">−</button>
                <button type="button" data-action="stage-x-plus">+</button>
              </div>
            </div>

            <div class="mini-dial-section">
              <h4>Y position</h4>

              <div
                id="stageYDial"
                class="dial small"
                tabindex="0"
                role="slider"
                aria-label="Stage Y position"
              ></div>

              <div class="mini-buttons">
                <button type="button" data-action="stage-y-minus">−</button>
                <button type="button" data-action="stage-y-plus">+</button>
              </div>
            </div>
          </div>
        </article>

        <!-- Focus controls -->
        <article class="control-card">
          <h3>3. Focus Adjustment</h3>
          <p>
            Use coarse adjustment at low power, followed by fine adjustment.
          </p>

          <div class="dual-dials">
            <div class="mini-dial-section">
              <h4>Coarse</h4>

              <div
                id="coarseDial"
                class="dial"
                tabindex="0"
                role="slider"
                aria-label="Coarse adjustment"
              ></div>

              <div class="mini-buttons">
                <button type="button" data-action="coarse-minus">−</button>
                <button type="button" data-action="coarse-plus">+</button>
              </div>
            </div>

            <div class="mini-dial-section">
              <h4>Fine</h4>

              <div
                id="fineDial"
                class="dial small"
                tabindex="0"
                role="slider"
                aria-label="Fine adjustment"
              ></div>

              <div class="mini-buttons">
                <button type="button" data-action="fine-minus">−</button>
                <button type="button" data-action="fine-plus">+</button>
              </div>
            </div>
          </div>
        </article>

        <!-- Light controls -->
        <article class="control-card">
          <h3>4. Illumination</h3>
          <p>
            Switch on the lamp and adjust the intensity for a clear image.
          </p>

          <div class="light-control">
            <button id="powerButton" class="power-button" type="button">
              Light: OFF
            </button>

            <input
              id="lightSlider"
              type="range"
              min="0"
              max="100"
              value="45"
              aria-label="Light intensity"
            />

            <div class="light-output">
              <span>Dim</span>
              <strong id="lightSliderValue">45%</strong>
              <span>Bright</span>
            </div>
          </div>
        </article>
      </div>

      <div class="keyboard-help">
        <strong>Keyboard shortcuts:</strong>
        <kbd>←</kbd><kbd>→</kbd> stage X,
        <kbd>↑</kbd><kbd>↓</kbd> stage Y,
        <kbd>C</kbd>/<kbd>V</kbd> coarse focus,
        <kbd>F</kbd>/<kbd>G</kbd> fine focus,
        <kbd>1</kbd> low power,
        <kbd>2</kbd> medium power,
        <kbd>3</kbd> high power.
      </div>
    </section>
  </main>

  <!-- Completion dialog -->
  <div
    id="completionModal"
    class="modal"
    role="dialog"
    aria-modal="true"
    aria-labelledby="completionTitle"
  >
    <div class="modal-card">
      <div class="modal-icon">✓</div>

      <h2 id="completionTitle">Microscope procedure completed</h2>

      <p>
        You successfully centred and focused the specimen under low power,
        changed to high power, and used fine adjustment to obtain a clear
        image.
      </p>

      <div id="finalScore" class="score">Final score: 100 / 100</div>

      <button id="closeModalButton" class="button" type="button">
        Continue exploring
      </button>

      <button id="restartModalButton" class="button secondary" type="button">
        Restart
      </button>
    </div>
  </div>

  <script>
    "use strict";

    const objectives = [
      {
        name: "Low power",
        power: 4,
        total: 40,
        scale: 1,
        noseAngle: 0,
        colour: "#d84b4b"
      },
      {
        name: "Medium power",
        power: 10,
        total: 100,
        scale: 2.2,
        noseAngle: 58,
        colour: "#f1c84b"
      },
      {
        name: "High power",
        power: 40,
        total: 400,
        scale: 6.2,
        noseAngle: -58,
        colour: "#478fd0"
      }
    ];

    const instructions = [
      {
        title: "Step 1: Switch on the light",
        text:
          "Switch on the illuminator and set the light to a moderate intensity, about 35–70%."
      },
      {
        title: "Step 2: Begin with low power",
        text:
          "Make sure the 4× objective is positioned over the specimen. Microscopy should begin under low power."
      },
      {
        title: "Step 3: Centre the specimen",
        text:
          "Rotate the X and Y stage-control knobs until the specimen is near the centre of the field of view."
      },
      {
        title: "Step 4: Use coarse adjustment",
        text:
          "Under low power, rotate the coarse adjustment knob until cell outlines become visible."
      },
      {
        title: "Step 5: Use fine adjustment",
        text:
          "Rotate the fine adjustment knob to obtain a sharp and detailed low-power image."
      },
      {
        title: "Step 6: Change to high power",
        text:
          "Rotate the nosepiece until the 40× objective clicks into position. Do not use coarse adjustment after this."
      },
      {
        title: "Step 7: Re-centre the image",
        text:
          "The field of view is smaller under high power. Use the stage controls to place the cells in the centre."
      },
      {
        title: "Step 8: Fine-focus under high power",
        text:
          "Use only the fine adjustment knob to obtain a sharp high-power image."
      },
      {
        title: "Procedure complete",
        text:
          "You have correctly used the microscope from low power to high power."
      }
    ];

    const initialState = {
      objective: 0,
      stageX: 30,
      stageY: -24,
      focus: -14,
      lightOn: false,
      light: 45,
      currentStep: 0,
      score: 100,
      completed: Array(8).fill(false),
      modalShown: false,
      coarseAngle: 0,
      fineAngle: 0,
      stageXAngle: 0,
      stageYAngle: 0,
      labelsVisible: true
    };

    let state = structuredClone(initialState);

    const canvas = document.getElementById("viewCanvas");
    const ctx = canvas.getContext("2d");

    const bufferCanvas = document.createElement("canvas");
    const bufferCtx = bufferCanvas.getContext("2d");

    const objectiveBadge =
      document.getElementById("objectiveBadge");
    const totalMagnificationBadge =
      document.getElementById("totalMagnificationBadge");
    const objectiveReadout =
      document.getElementById("objectiveReadout");
    const stageReadout =
      document.getElementById("stageReadout");
    const focusReadout =
      document.getElementById("focusReadout");
    const lightReadout =
      document.getElementById("lightReadout");
    const lightSliderValue =
      document.getElementById("lightSliderValue");
    const focusMeterFill =
      document.getElementById("focusMeterFill");
    const viewerMessage =
      document.getElementById("viewerMessage");

    const nosepieceGroup =
      document.getElementById("nosepieceGroup");
    const stageGroup =
      document.getElementById("stageGroup");
    const stageSlide =
      document.getElementById("stageSlide");
    const lightBeam =
      document.getElementById("lightBeam");
    const lampGlow =
      document.getElementById("lampGlow");
    const lampSurface =
      document.getElementById("lampSurface");

    const coarseSvgKnob =
      document.getElementById("coarseSvgKnob");
    const fineSvgKnob =
      document.getElementById("fineSvgKnob");
    const stageXSvgKnob =
      document.getElementById("stageXSvgKnob");
    const stageYSvgKnob =
      document.getElementById("stageYSvgKnob");

    const coarseDial =
      document.getElementById("coarseDial");
    const fineDial =
      document.getElementById("fineDial");
    const stageXDial =
      document.getElementById("stageXDial");
    const stageYDial =
      document.getElementById("stageYDial");

    const lightSlider =
      document.getElementById("lightSlider");
    const powerButton =
      document.getElementById("powerButton");

    const instructionTitle =
      document.getElementById("instructionTitle");
    const instructionText =
      document.getElementById("instructionText");
    const progressFill =
      document.getElementById("progressFill");
    const feedbackBox =
      document.getElementById("feedbackBox");
    const scoreBadge =
      document.getElementById("scoreBadge");

    const completionModal =
      document.getElementById("completionModal");
    const finalScore =
      document.getElementById("finalScore");

    function clamp(value, min, max) {
      return Math.max(min, Math.min(max, value));
    }

    function signed(value, digits = 0) {
      const number = Number(value);

      if (Math.abs(number) < Math.pow(10, -digits) / 2) {
        return Number(0).toFixed(digits);
      }

      return `${number > 0 ? "+" : "−"}${Math.abs(number).toFixed(digits)}`;
    }

    function hash(x, y, seed = 0) {
      const value = Math.sin(
        x * 127.1 + y * 311.7 + seed * 74.7
      ) * 43758.5453;

      return value - Math.floor(value);
    }

    function getTargetFocus() {
      /*
        A slightly uneven specimen means the ideal focus changes a little
        as the slide moves.
      */
      return (
        Math.sin(state.stageX * 0.08) * 0.38 +
        Math.cos(state.stageY * 0.09) * 0.3
      );
    }

    function getFocusError() {
      return Math.abs(state.focus - getTargetFocus());
    }

    function getSharpness() {
      const error = getFocusError();
      const sensitivity =
        state.objective === 2 ? 1.7 :
        state.objective === 1 ? 1.05 : 0.65;

      return clamp(100 - error * 16 * sensitivity, 0, 100);
    }

    function isSpecimenCentred(tolerance) {
      return (
        Math.abs(state.stageX) <= tolerance &&
        Math.abs(state.stageY) <= tolerance
      );
    }

    function setFeedback(message, type = "") {
      feedbackBox.textContent = message;
      feedbackBox.className =
        `feedback-box${type ? ` ${type}` : ""}`;
    }

    function losePoints(points, reason) {
      state.score = Math.max(0, state.score - points);
      scoreBadge.textContent = `Score: ${state.score}`;
      setFeedback(`${reason} −${points} points.`, "warning");
    }

    function completeStep(index, feedback) {
      if (state.completed[index]) {
        return;
      }

      state.completed[index] = true;

      if (index === state.currentStep) {
        while (
          state.currentStep < state.completed.length &&
          state.completed[state.currentStep]
        ) {
          state.currentStep += 1;
        }
      }

      setFeedback(feedback, "success");
      updateGuide();

      if (state.currentStep >= 8 && !state.modalShown) {
        state.modalShown = true;

        window.setTimeout(() => {
          finalScore.textContent =
            `Final score: ${state.score} / 100`;
          completionModal.classList.add("show");
        }, 500);
      }
    }

    function evaluateProgress(action) {
      switch (state.currentStep) {
        case 0:
          if (
            state.lightOn &&
            state.light >= 35 &&
            state.light <= 70
          ) {
            completeStep(
              0,
              "Good. The lamp is on at a suitable intensity."
            );
          }
          break;

        case 1:
          if (state.objective === 0 && action === "objective") {
            completeStep(
              1,
              "Correct. Always begin with the low-power objective."
            );
          }
          break;

        case 2:
          if (
            state.objective === 0 &&
            isSpecimenCentred(9) &&
            action === "stage"
          ) {
            completeStep(
              2,
              "The specimen is now centred in the field of view."
            );
          }
          break;

        case 3:
          if (
            state.objective === 0 &&
            getFocusError() <= 3.2 &&
            action === "coarse"
          ) {
            completeStep(
              3,
              "Approximate focus obtained. Now use fine adjustment."
            );
          }
          break;

        case 4:
          if (
            state.objective === 0 &&
            getFocusError() <= 0.75 &&
            action === "fine"
          ) {
            completeStep(
              4,
              "The low-power image is sharply focused."
            );
          }
          break;

        case 5:
          if (
            state.objective === 2 &&
            action === "objective"
          ) {
            completeStep(
              5,
              "High power selected. Use only the fine adjustment knob."
            );
          }
          break;

        case 6:
          if (
            state.objective === 2 &&
            isSpecimenCentred(4.5) &&
            action === "stage"
          ) {
            completeStep(
              6,
              "The specimen is centred under high power."
            );
          }
          break;

        case 7:
          if (
            state.objective === 2 &&
            getFocusError() <= 0.32 &&
            action === "fine"
          ) {
            completeStep(
              7,
              "Excellent. A sharp high-power image has been obtained."
            );
          }
          break;
      }

      updateGuide();
    }

    function updateGuide() {
      const instruction =
        instructions[Math.min(state.currentStep, 8)];

      instructionTitle.textContent = instruction.title;
      instructionText.textContent = instruction.text;

      const completedCount =
        state.completed.filter(Boolean).length;

      progressFill.style.width =
        `${(completedCount / 8) * 100}%`;

      const stepElements =
        document.querySelectorAll(".step");

      stepElements.forEach((element, index) => {
        element.classList.toggle(
          "complete",
          state.completed[index]
        );

        element.classList.toggle(
          "active",
          index === state.currentStep
        );

        const number = element.querySelector(".step-number");
        number.textContent =
          state.completed[index] ? "✓" : String(index + 1);
      });

      scoreBadge.textContent = `Score: ${state.score}`;
    }

    function setObjective(index, userAction = true) {
      index = clamp(index, 0, objectives.length - 1);

      const oldObjective = state.objective;
      state.objective = index;

      /*
        Parfocal objectives retain approximate focus, but higher power
        requires additional fine adjustment.
      */
      if (index === 2 && oldObjective !== 2) {
        state.focus += 0.65;
      }

      nosepieceGroup.style.transform =
        `rotate(${objectives[index].noseAngle}deg)`;

      document
        .querySelectorAll(".objective-button")
        .forEach((button, buttonIndex) => {
          button.classList.toggle(
            "active",
            buttonIndex === index
          );
        });

      if (
        userAction &&
        index === 2 &&
        !state.completed[4]
      ) {
        losePoints(
          5,
          "The image should be centred and focused under low power before changing to high power"
        );
      } else if (userAction) {
        setFeedback(
          `${objectives[index].name} ${objectives[index].power}× objective selected.`
        );
      }

      updateAll();
      evaluateProgress("objective");
    }

    function cycleObjective() {
      setObjective((state.objective + 1) % objectives.length);
    }

    function changeStage(axis, amount) {
      if (axis === "x") {
        state.stageX =
          clamp(state.stageX + amount, -50, 50);
        state.stageXAngle += amount * 7;
      } else {
        state.stageY =
          clamp(state.stageY + amount, -50, 50);
        state.stageYAngle += amount * 7;
      }

      updateAll();
      evaluateProgress("stage");
    }

    function changeFocus(type, amount) {
      if (type === "coarse") {
        if (state.objective === 2) {
          losePoints(
            4,
            "Do not use coarse adjustment under high power because the objective may hit the slide"
          );

          updateAll();
          return;
        }

        state.focus =
          clamp(state.focus + amount, -20, 20);
        state.coarseAngle += amount * 22;

        updateAll();
        evaluateProgress("coarse");
      } else {
        state.focus =
          clamp(state.focus + amount, -20, 20);
        state.fineAngle += amount * 80;

        updateAll();
        evaluateProgress("fine");
      }
    }

    function toggleLight() {
      state.lightOn = !state.lightOn;

      setFeedback(
        state.lightOn
          ? "The illuminator is switched on."
          : "The illuminator is switched off."
      );

      updateAll();
      evaluateProgress("light");
    }

    function setLight(value) {
      state.light = clamp(Number(value), 0, 100);

      if (state.lightOn) {
        if (state.light < 25) {
          setFeedback(
            "The illumination is too dim. Increase the light intensity.",
            "warning"
          );
        } else if (state.light > 80) {
          setFeedback(
            "The illumination is too bright. Excessive light reduces contrast.",
            "warning"
          );
        } else {
          setFeedback(
            "The illumination level is suitable.",
            "success"
          );
        }
      }

      updateAll();
      evaluateProgress("light");
    }

    function drawRoundedCell(
      context,
      x,
      y,
      width,
      height,
      column,
      row,
      scale
    ) {
      const jitter1 = (hash(column, row, 1) - 0.5) * 8 * scale;
      const jitter2 = (hash(column, row, 2) - 0.5) * 7 * scale;
      const jitter3 = (hash(column, row, 3) - 0.5) * 8 * scale;
      const jitter4 = (hash(column, row, 4) - 0.5) * 7 * scale;

      context.beginPath();
      context.moveTo(x + jitter1, y + height * 0.1);
      context.quadraticCurveTo(
        x + width * 0.45,
        y + jitter2,
        x + width + jitter3,
        y + height * 0.12
      );
      context.quadraticCurveTo(
        x + width + jitter4,
        y + height * 0.5,
        x + width + jitter1,
        y + height * 0.9
      );
      context.quadraticCurveTo(
        x + width * 0.55,
        y + height + jitter3,
        x + jitter2,
        y + height * 0.88
      );
      context.quadraticCurveTo(
        x + jitter4,
        y + height * 0.5,
        x + jitter1,
        y + height * 0.1
      );
      context.closePath();

      const fillHue =
        310 + (hash(column, row, 5) - 0.5) * 20;

      context.fillStyle =
        `hsla(${fillHue}, 46%, 80%, 0.42)`;
      context.fill();

      context.lineWidth =
        clamp(1.4 * Math.sqrt(scale), 1.2, 6);

      context.strokeStyle =
        "rgba(105, 41, 111, 0.78)";
      context.stroke();

      /* Cytoplasm lining */
      context.beginPath();
      context.ellipse(
        x + width * 0.5,
        y + height * 0.5,
        width * 0.38,
        height * 0.32,
        (hash(column, row, 6) - 0.5) * 0.15,
        0,
        Math.PI * 2
      );

      context.strokeStyle =
        "rgba(170, 94, 165, 0.18)";
      context.lineWidth =
        clamp(0.7 * Math.sqrt(scale), 0.6, 3);
      context.stroke();

      /* Nucleus */
      const nucleusX =
        x + width * (0.32 + hash(column, row, 7) * 0.35);
      const nucleusY =
        y + height * (0.3 + hash(column, row, 8) * 0.4);

      context.beginPath();
      context.ellipse(
        nucleusX,
        nucleusY,
        clamp(width * 0.07, 2, 18),
        clamp(height * 0.1, 2, 15),
        hash(column, row, 9) * Math.PI,
        0,
        Math.PI * 2
      );

      context.fillStyle = "rgba(92, 30, 105, 0.68)";
      context.fill();

      context.beginPath();
      context.arc(
        nucleusX - width * 0.015,
        nucleusY - height * 0.018,
        clamp(width * 0.014, 0.7, 4),
        0,
        Math.PI * 2
      );

      context.fillStyle = "rgba(245, 201, 243, 0.7)";
      context.fill();
    }

    function renderMicroscopeView() {
      const rect = canvas.getBoundingClientRect();
      const cssSize = Math.max(
        280,
        Math.min(rect.width || 600, rect.height || 600)
      );

      const dpr = Math.min(window.devicePixelRatio || 1, 2);

      canvas.width = Math.round(cssSize * dpr);
      canvas.height = Math.round(cssSize * dpr);

      bufferCanvas.width = canvas.width;
      bufferCanvas.height = canvas.height;

      const width = canvas.width;
      const height = canvas.height;
      const radius = Math.min(width, height) * 0.475;

      ctx.setTransform(1, 0, 0, 1, 0, 0);
      bufferCtx.setTransform(1, 0, 0, 1, 0, 0);

      ctx.clearRect(0, 0, width, height);
      bufferCtx.clearRect(0, 0, width, height);

      ctx.fillStyle = "#020607";
      ctx.fillRect(0, 0, width, height);

      if (!state.lightOn || state.light <= 1) {
        const darkGradient = ctx.createRadialGradient(
          width / 2,
          height / 2,
          0,
          width / 2,
          height / 2,
          radius
        );

        darkGradient.addColorStop(0, "#11191c");
        darkGradient.addColorStop(0.75, "#04090b");
        darkGradient.addColorStop(1, "#000");

        ctx.beginPath();
        ctx.arc(
          width / 2,
          height / 2,
          radius,
          0,
          Math.PI * 2
        );
        ctx.fillStyle = darkGradient;
        ctx.fill();

        return;
      }

      const objective = objectives[state.objective];
      const scale = objective.scale * dpr;

      /*
        Positive stage movement produces an opposite apparent movement
        of the specimen through the eyepiece.
      */
      const offsetX =
        -state.stageX * 4.4 * scale;
      const offsetY =
        state.stageY * 4.4 * scale;

      const centreX = width / 2 + offsetX;
      const centreY = height / 2 + offsetY;

      const lightRatio = state.light / 100;

      const backgroundLightness =
        39 + lightRatio * 48;

      const background = bufferCtx.createRadialGradient(
        width / 2,
        height / 2,
        0,
        width / 2,
        height / 2,
        radius
      );

      background.addColorStop(
        0,
        `hsl(318, 48%, ${backgroundLightness}%)`
      );

      background.addColorStop(
        0.75,
        `hsl(310, 36%, ${backgroundLightness - 5}%)`
      );

      background.addColorStop(
        1,
        `hsl(302, 30%, ${backgroundLightness - 13}%)`
      );

      bufferCtx.fillStyle = background;
      bufferCtx.fillRect(0, 0, width, height);

      const cellWidth = 69 * scale;
      const cellHeight = 43 * scale;
      const columns = 16;
      const rows = 18;

      for (let row = -rows; row <= rows; row += 1) {
        for (
          let column = -columns;
          column <= columns;
          column += 1
        ) {
          const rowShift =
            Math.abs(row) % 2 === 1 ? cellWidth * 0.28 : 0;

          const x =
            centreX +
            column * cellWidth +
            rowShift -
            cellWidth / 2;

          const y =
            centreY +
            row * cellHeight -
            cellHeight / 2;

          if (
            x < -cellWidth ||
            x > width + cellWidth ||
            y < -cellHeight ||
            y > height + cellHeight
          ) {
            continue;
          }

          drawRoundedCell(
            bufferCtx,
            x,
            y,
            cellWidth * 0.96,
            cellHeight * 0.95,
            column,
            row,
            scale
          );
        }
      }

      const focusError = getFocusError();

      const blurMultiplier =
        state.objective === 2 ? 2.35 :
        state.objective === 1 ? 1.35 : 0.72;

      const blur =
        clamp(focusError * blurMultiplier * dpr, 0, 30);

      ctx.save();

      ctx.beginPath();
      ctx.arc(
        width / 2,
        height / 2,
        radius,
        0,
        Math.PI * 2
      );
      ctx.clip();

      ctx.filter = `blur(${blur}px)`;
      ctx.drawImage(bufferCanvas, 0, 0);
      ctx.filter = "none";

      /*
        Very bright illumination washes out contrast.
      */
      if (state.light > 78) {
        const washout =
          ((state.light - 78) / 22) * 0.55;

        ctx.fillStyle =
          `rgba(255,255,245,${washout})`;
        ctx.fillRect(0, 0, width, height);
      }

      /*
        Very dim illumination darkens the field.
      */
      if (state.light < 30) {
        const darkness =
          ((30 - state.light) / 30) * 0.72;

        ctx.fillStyle =
          `rgba(10,2,13,${darkness})`;
        ctx.fillRect(0, 0, width, height);
      }

      ctx.restore();

      const vignette = ctx.createRadialGradient(
        width / 2,
        height / 2,
        radius * 0.62,
        width / 2,
        height / 2,
        radius
      );

      vignette.addColorStop(0, "rgba(0,0,0,0)");
      vignette.addColorStop(0.84, "rgba(0,0,0,0.06)");
      vignette.addColorStop(1, "rgba(0,0,0,0.92)");

      ctx.beginPath();
      ctx.arc(
        width / 2,
        height / 2,
        radius,
        0,
        Math.PI * 2
      );
      ctx.fillStyle = vignette;
      ctx.fill();

      ctx.beginPath();
      ctx.arc(
        width / 2,
        height / 2,
        radius,
        0,
        Math.PI * 2
      );
      ctx.strokeStyle = "#1a2c33";
      ctx.lineWidth = 8 * dpr;
      ctx.stroke();
    }

    function updateViewerMessage() {
      const focusError = getFocusError();
      const centreTolerance =
        state.objective === 2 ? 4.5 : 9;

      let message = "";

      if (!state.lightOn) {
        message = "Switch on the light.";
      } else if (state.light < 22) {
        message = "The light is too dim.";
      } else if (state.light > 85) {
        message = "The light is too bright.";
      } else if (!isSpecimenCentred(centreTolerance * 2.2)) {
        message = "Move the stage to locate the specimen.";
      } else if (focusError > 5) {
        message =
          state.objective === 2
            ? "Use fine adjustment only."
            : "Use coarse adjustment.";
      } else if (focusError > 0.8) {
        message = "Use fine adjustment.";
      } else if (
        state.objective === 2 &&
        focusError > 0.32
      ) {
        message = "Fine-focus carefully.";
      } else {
        message = "Clear image obtained.";
      }

      viewerMessage.textContent = message;
    }

    function updateMicroscopeDiagram() {
      /*
        Stage appears to move slightly vertically as focus changes.
      */
      const stageZ = clamp(state.focus * -0.32, -7, 7);

      stageGroup.style.transform =
        `translate(0px, ${stageZ}px)`;

      stageSlide.style.transform =
        `translate(${state.stageX * 0.28}px, ${state.stageY * 0.16}px)`;

      const lightOpacity =
        state.lightOn ? 0.1 + state.light / 125 : 0;

      lightBeam.style.opacity =
        String(clamp(lightOpacity, 0, 0.9));

      lampGlow.style.opacity =
        String(state.lightOn ? state.light / 100 : 0);

      lampSurface.setAttribute(
        "fill",
        state.lightOn
          ? `hsl(48, 100%, ${55 + state.light * 0.35}%)`
          : "#4d5d63"
      );

      coarseSvgKnob.style.transform =
        `rotate(${state.coarseAngle}deg)`;

      fineSvgKnob.style.transform =
        `rotate(${state.fineAngle}deg)`;

      stageXSvgKnob.style.transform =
        `rotate(${state.stageXAngle}deg)`;

      stageYSvgKnob.style.transform =
        `rotate(${state.stageYAngle}deg)`;
    }

    function updateDialGraphics() {
      coarseDial.style.transform =
        `rotate(${state.coarseAngle}deg)`;

      fineDial.style.transform =
        `rotate(${state.fineAngle}deg)`;

      stageXDial.style.transform =
        `rotate(${state.stageXAngle}deg)`;

      stageYDial.style.transform =
        `rotate(${state.stageYAngle}deg)`;

      stageXDial.setAttribute(
        "aria-valuenow",
        state.stageX.toFixed(1)
      );

      stageYDial.setAttribute(
        "aria-valuenow",
        state.stageY.toFixed(1)
      );

      coarseDial.setAttribute(
        "aria-valuenow",
        state.focus.toFixed(1)
      );

      fineDial.setAttribute(
        "aria-valuenow",
        state.focus.toFixed(1)
      );
    }

    function updateReadouts() {
      const objective = objectives[state.objective];
      const sharpness = getSharpness();

      objectiveBadge.textContent =
        `${objective.name}: ${objective.power}×`;

      objectiveBadge.style.background =
        objective.colour;

      totalMagnificationBadge.textContent =
        `Total magnification: ${objective.total}×`;

      objectiveReadout.textContent =
        `${objective.power}× ${objective.name.toLowerCase()}`;

      stageReadout.textContent =
        `${signed(state.stageX)} / ${signed(state.stageY)}`;

      focusReadout.textContent =
        signed(state.focus, 1);

      lightReadout.textContent =
        state.lightOn ? `${Math.round(state.light)}%` : "Off";

      lightSliderValue.textContent =
        `${Math.round(state.light)}%`;

      lightSlider.value = state.light;

      powerButton.textContent =
        `Light: ${state.lightOn ? "ON" : "OFF"}`;

      powerButton.classList.toggle(
        "on",
        state.lightOn
      );

      focusMeterFill.style.width =
        `${sharpness}%`;

      if (sharpness >= 88) {
        focusMeterFill.style.background =
          "var(--success)";
      } else if (sharpness >= 45) {
        focusMeterFill.style.background =
          "var(--warning)";
      } else {
        focusMeterFill.style.background =
          "var(--danger)";
      }
    }

    function updateAll() {
      updateReadouts();
      updateDialGraphics();
      updateMicroscopeDiagram();
      updateViewerMessage();
      renderMicroscopeView();
      updateGuide();
    }

    function createRotaryControl(element, onChange) {
      let dragging = false;
      let lastY = 0;
      let lastX = 0;

      element.addEventListener("pointerdown", event => {
        dragging = true;
        lastY = event.clientY;
        lastX = event.clientX;
        element.setPointerCapture(event.pointerId);
        event.preventDefault();
      });

      element.addEventListener("pointermove", event => {
        if (!dragging) {
          return;
        }

        const verticalDelta =
          lastY - event.clientY;
        const horizontalDelta =
          event.clientX - lastX;

        const movement =
          verticalDelta + horizontalDelta;

        if (Math.abs(movement) >= 0.5) {
          onChange(movement);
        }

        lastY = event.clientY;
        lastX = event.clientX;
      });

      const stopDragging = event => {
        dragging = false;

        if (
          event.pointerId !== undefined &&
          element.hasPointerCapture(event.pointerId)
        ) {
          element.releasePointerCapture(event.pointerId);
        }
      };

      element.addEventListener(
        "pointerup",
        stopDragging
      );

      element.addEventListener(
        "pointercancel",
        stopDragging
      );

      element.addEventListener("wheel", event => {
        event.preventDefault();

        const movement =
          event.deltaY < 0 ? 2 : -2;

        onChange(movement);
      }, { passive: false });

      element.addEventListener("keydown", event => {
        if (
          event.key === "ArrowUp" ||
          event.key === "ArrowRight"
        ) {
          event.preventDefault();
          onChange(2);
        }

        if (
          event.key === "ArrowDown" ||
          event.key === "ArrowLeft"
        ) {
          event.preventDefault();
          onChange(-2);
        }
      });
    }

    createRotaryControl(stageXDial, movement => {
      changeStage("x", movement * 0.18);
    });

    createRotaryControl(stageYDial, movement => {
      changeStage("y", movement * 0.18);
    });

    createRotaryControl(coarseDial, movement => {
      changeFocus("coarse", movement * 0.075);
    });

    createRotaryControl(fineDial, movement => {
      changeFocus("fine", movement * 0.012);
    });

    document
      .querySelectorAll(".objective-button")
      .forEach(button => {
        button.addEventListener("click", () => {
          setObjective(
            Number(button.dataset.objective)
          );
        });
      });

    document
      .getElementById("nosepieceClickable")
      .addEventListener("click", cycleObjective);

    document
      .getElementById("nosepieceClickable")
      .addEventListener("keydown", event => {
        if (
          event.key === "Enter" ||
          event.key === " "
        ) {
          event.preventDefault();
          cycleObjective();
        }
      });

    document
      .querySelectorAll("[data-action]")
      .forEach(button => {
        button.addEventListener("click", () => {
          const action = button.dataset.action;

          switch (action) {
            case "stage-x-minus":
              changeStage("x", -2);
              break;

            case "stage-x-plus":
              changeStage("x", 2);
              break;

            case "stage-y-minus":
              changeStage("y", -2);
              break;

            case "stage-y-plus":
              changeStage("y", 2);
              break;

            case "coarse-minus":
              changeFocus("coarse", -1.5);
              break;

            case "coarse-plus":
              changeFocus("coarse", 1.5);
              break;

            case "fine-minus":
              changeFocus("fine", -0.15);
              break;

            case "fine-plus":
              changeFocus("fine", 0.15);
              break;
          }
        });
      });

    powerButton.addEventListener("click", toggleLight);

    lightSlider.addEventListener("input", event => {
      setLight(event.target.value);
    });

    document.addEventListener("keydown", event => {
      const targetTag =
        event.target.tagName.toLowerCase();

      if (
        targetTag === "input" ||
        targetTag === "button"
      ) {
        return;
      }

      switch (event.key.toLowerCase()) {
        case "arrowleft":
          event.preventDefault();
          changeStage("x", -1);
          break;

        case "arrowright":
          event.preventDefault();
          changeStage("x", 1);
          break;

        case "arrowup":
          event.preventDefault();
          changeStage("y", 1);
          break;

        case "arrowdown":
          event.preventDefault();
          changeStage("y", -1);
          break;

        case "c":
          changeFocus("coarse", -1);
          break;

        case "v":
          changeFocus("coarse", 1);
          break;

        case "f":
          changeFocus("fine", -0.12);
          break;

        case "g":
          changeFocus("fine", 0.12);
          break;

        case "1":
          setObjective(0);
          break;

        case "2":
          setObjective(1);
          break;

        case "3":
          setObjective(2);
          break;
      }
    });

    function resetSimulation() {
      state = structuredClone(initialState);

      completionModal.classList.remove("show");

      nosepieceGroup.style.transform =
        `rotate(${objectives[0].noseAngle}deg)`;

      setFeedback(
        "Follow the procedure in order. Begin by switching on the light."
      );

      updateAll();
    }

    document
      .getElementById("resetButton")
      .addEventListener("click", resetSimulation);

    document
      .getElementById("closeModalButton")
      .addEventListener("click", () => {
        completionModal.classList.remove("show");
      });

    document
      .getElementById("restartModalButton")
      .addEventListener("click", resetSimulation);

    document
      .getElementById("toggleLabelsButton")
      .addEventListener("click", event => {
        state.labelsVisible = !state.labelsVisible;

        document.getElementById(
          "microscopeLabels"
        ).style.display =
          state.labelsVisible ? "" : "none";

        event.currentTarget.textContent =
          state.labelsVisible
            ? "Hide labels"
            : "Show labels";
      });

    completionModal.addEventListener("click", event => {
      if (event.target === completionModal) {
        completionModal.classList.remove("show");
      }
    });

    let resizeTimer;

    window.addEventListener("resize", () => {
      clearTimeout(resizeTimer);
      resizeTimer = setTimeout(renderMicroscopeView, 100);
    });

    /*
      Initial rendering must wait until layout dimensions are available.
    */
    window.addEventListener("load", () => {
      updateAll();
    });

    updateAll();
  </script>
</body>
</html>
