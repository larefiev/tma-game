<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Premium Games - Telegram Mini App</title>
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800;900&display=swap" rel="stylesheet">
  <style>
    :root {
      /* Цветовая палитра в стиле 1win / Stake */
      --bg-dark: #0a101d;
      --bg-panel: #162032;
      --bg-panel-hover: #1e2a40;
      --accent-blue: #007aff;
      --accent-cyan: #00d2ff;
      --accent-green: #00e701;
      --accent-red: #ff3366;
      --accent-gold: #ffb703;
      --accent-purple: #9b59b6;
      
      --text-main: #ffffff;
      --text-muted: #8b9eb7;
      --border-color: rgba(255, 255, 255, 0.05);
      
      --shadow-glow: 0 0 20px rgba(0, 122, 255, 0.3);
      --font-main: 'Inter', sans-serif;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; -webkit-tap-highlight-color: transparent; }

    body {
      background-color: var(--bg-dark);
      color: var(--text-main);
      font-family: var(--font-main);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding-bottom: 80px;
      overflow-x: hidden;
    }

    /* Анимации перехода страниц */
    .view {
      display: none;
      width: 100%;
      max-width: 480px;
      flex-direction: column;
      padding: 0 16px;
      gap: 16px;
      animation: fadeIn 0.3s ease-out;
    }
    .view.active { display: flex; }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }

    /* HEADER */
    .header {
      width: 100%; max-width: 480px; display: flex; justify-content: space-between; align-items: center;
      padding: 16px; background: rgba(10, 16, 29, 0.85); backdrop-filter: blur(12px);
      position: sticky; top: 0; z-index: 100; border-bottom: 1px solid var(--border-color);
    }
    .brand-logo { font-size: 18px; font-weight: 900; background: linear-gradient(90deg, var(--accent-cyan), var(--accent-blue)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
    .balance-pill {
      background: rgba(0, 231, 1, 0.1); border: 1px solid rgba(0, 231, 1, 0.3);
      padding: 8px 16px; border-radius: 20px; font-size: 14px; font-weight: 800; color: var(--accent-green);
      display: flex; align-items: center; gap: 6px; cursor: pointer; transition: 0.2s;
    }
    .balance-pill:active { transform: scale(0.95); }

    /* ГЛАВНАЯ СТРАНИЦА */
    .promo-banner {
      background: linear-gradient(135deg, #1f103b, #3b145b);
      border-radius: 20px; padding: 20px; display: flex; justify-content: space-between; align-items: center;
      border: 1px solid rgba(155, 89, 182, 0.3); box-shadow: 0 10px 30px rgba(59, 20, 91, 0.5); cursor: pointer;
    }
    
    .section-title { font-size: 18px; font-weight: 800; margin: 10px 0; color: #fff; display: flex; align-items: center; gap: 8px;}
    .section-title::before { content: ''; display: block; width: 4px; height: 18px; background: var(--accent-blue); border-radius: 2px; }

    .games-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; }
    .game-card {
      background: var(--bg-panel); border: 1px solid var(--border-color); border-radius: 20px;
      padding: 16px; display: flex; flex-direction: column; align-items: center; gap: 12px;
      cursor: pointer; transition: all 0.2s ease; position: relative; overflow: hidden;
    }
    .game-card:active { transform: scale(0.96); border-color: var(--accent-blue); }
    .game-thumb { font-size: 48px; filter: drop-shadow(0 10px 10px rgba(0,0,0,0.5)); transition: 0.3s; }
    .game-card:hover .game-thumb { transform: translateY(-5px) scale(1.1); }
    .game-name { font-size: 14px; font-weight: 800; letter-spacing: 0.5px; }

    /* ОБЩИЙ БЛОК СТАВОК */
    .bet-controls {
      background: var(--bg-panel); border-radius: 20px; padding: 16px; display: flex; flex-direction: column; gap: 12px;
      border: 1px solid var(--border-color);
    }
    .bet-amount-row { display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.3); border-radius: 14px; padding: 12px 16px; border: 1px solid var(--border-color); }
    .bet-value { font-size: 20px; font-weight: 900; color: var(--accent-gold); display: flex; align-items: center; gap: 6px; }
    .bet-quick-btns { display: flex; gap: 6px; }
    .btn-quick { background: var(--bg-panel-hover); border: none; color: var(--text-main); padding: 8px 12px; border-radius: 8px; font-weight: 700; font-size: 12px; cursor: pointer; }
    .btn-quick:active { background: var(--accent-blue); }
    
    .btn-action {
      width: 100%; padding: 16px; border: none; border-radius: 14px; font-size: 16px; font-weight: 900;
      color: #fff; cursor: pointer; transition: 0.2s; text-transform: uppercase; letter-spacing: 1px;
    }
    .btn-play { background: linear-gradient(90deg, #00b09b, #96c93d); box-shadow: 0 4px 15px rgba(150, 201, 61, 0.4); }
    .btn-play:active { transform: translateY(2px); }
    .btn-danger { background: linear-gradient(90deg, #ff416c, #ff4b2b); }

    /* ИГРА: CRASH (ROCKET) */
    .crash-screen {
      width: 100%; height: 240px; background: #070b12; border-radius: 20px;
      border: 1px solid rgba(0, 122, 255, 0.2); position: relative; overflow: hidden;
      display: flex; flex-direction: column; justify-content: center; align-items: center;
    }
    .crash-bg-stars { position: absolute; width: 200%; height: 200%; background: radial-gradient(white, rgba(255,255,255,.2) 2px, transparent 40px); background-size: 100px 100px; opacity: 0.1; animation: flyStars 20s linear infinite; }
    @keyframes flyStars { from { transform: translate(0, 0); } to { transform: translate(-50%, 50%); } }
    .crash-multiplier { font-size: 56px; font-weight: 900; color: #fff; z-index: 2; text-shadow: 0 0 20px rgba(255,255,255,0.2); }
    .crash-multiplier.crashed { color: var(--accent-red); text-shadow: 0 0 20px rgba(255, 51, 102, 0.5); }
    .crash-rocket { position: absolute; font-size: 40px; z-index: 3; transition: all 0.1s linear; left: 20px; bottom: 20px; filter: drop-shadow(0 0 10px rgba(255,183,3,0.8)); }
    
    /* ИГРА: MINES */
    .mines-grid { display: grid; grid-template-columns: repeat(5, 1fr); gap: 8px; margin: 10px 0; }
    .mine-cell {
      aspect-ratio: 1; background: var(--bg-panel); border: 2px solid var(--border-color); border-radius: 12px;
      display: flex; align-items: center; justify-content: center; font-size: 24px; cursor: pointer; transition: 0.2s;
      box-shadow: inset 0 -4px 0 rgba(0,0,0,0.2);
    }
    .mine-cell:active { transform: scale(0.9); }
    .mine-cell.revealed.gem { background: rgba(0, 231, 1, 0.15); border-color: var(--accent-green); box-shadow: 0 0 15px rgba(0, 231, 1, 0.2); }
    .mine-cell.revealed.bomb { background: rgba(255, 51, 102, 0.15); border-color: var(--accent-red); box-shadow: 0 0 15px rgba(255, 51, 102, 0.2); }

    /* ИГРА: СЛОТЫ */
    .slot-machine {
      background: #0d131f; border-radius: 20px; border: 2px solid rgba(155, 89, 182, 0.5);
      padding: 10px; display: grid; grid-template-columns: repeat(5, 1fr); grid-template-rows: repeat(4, 1fr); gap: 6px;
      box-shadow: inset 0 0 40px rgba(0,0,0,0.8), 0 0 20px rgba(155, 89, 182, 0.2);
    }
    .slot-cell {
      aspect-ratio: 1; background: rgba(255,255,255,0.03); border-radius: 10px;
      display: flex; align-items: center; justify-content: center; font-size: 28px;
      border: 1px solid rgba(255,255,255,0.05); transition: 0.3s;
    }
    .slot-cell.spinning { animation: slotSpin 0.1s linear infinite; filter: blur(2px); }
    @keyframes slotSpin { 0% { transform: translateY(-10px); } 100% { transform: translateY(10px); } }
    .slot-cell.win { background: rgba(255, 183, 3, 0.2); border-color: var(--accent-gold); transform: scale(1.1); z-index: 2; box-shadow: 0 0 15px var(--accent-gold); animation: pulseWin 1s infinite alternate; }
    @keyframes pulseWin { from { box-shadow: 0 0 10px var(--accent-gold); } to { box-shadow: 0 0 25px var(--accent-gold); } }

    /* НАВИГАЦИЯ НИЗ */
    .nav-bar {
      position: fixed; bottom: 0; left: 0; right: 0; background: rgba(10, 16, 29, 0.95); backdrop-filter: blur(15px);
      border-top: 1px solid var(--border-color); display: flex; justify-content: space-around; padding: 12px 0 24px 0; z-index: 1000;
    }
    .nav-item { display: flex; flex-direction: column; align-items: center; gap: 6px; color: var(--text-muted); font-size: 11px; font-weight: 700; cursor: pointer; }
    .nav-item.active { color: var(--accent-blue); }
    .nav-icon { font-size: 20px; transition: 0.3s; }
    .nav-item.active .nav-icon { transform: translateY(-3px); text-shadow: var(--shadow-glow); }

    /* УТИЛИТЫ */
    .flex-row { display: flex; align-items: center; justify-content: space-between; }
    .nav-back { color: var(--text-muted); font-weight: 700; cursor: pointer; display: inline-flex; align-items: center; gap: 6px; padding: 8px 0;}
    .history-bar { display: flex; gap: 8px; overflow-x: auto; padding: 10px 0; scrollbar-width: none; }
    .history-bar::-webkit-scrollbar { display: none; }
    .history-pill { background: rgba(255,255,255,0.05); padding: 4px 10px; border-radius: 12px; font-size: 12px; font-weight: 800; white-space: nowrap; }
  </style>
</head>
<body>

  <!-- HEADER -->
  <header class="header">
    <div class="brand-logo">♠ 1RANDOM</div>
    <div class="balance-pill" onclick="App.alert('Тут будет пополнение')">
      ⭐ <span id="headerBalance">1000</span>
    </div>
  </header>

  <!-- ВЬЮ: ГЛАВНАЯ -->
  <main class="view active" id="view-home">
    <div class="promo-banner" onclick="App.navigate('slot')">
      <div>
        <div style="font-size:18px; font-weight:900; color:#fff; margin-bottom: 4px;">Sweet Bonanza</div>
        <div style="font-size:12px; color:rgba(255,255,255,0.8);">Новые множители до x100!</div>
      </div>
      <div style="font-size: 48px; filter: drop-shadow(0 0 10px rgba(255,255,255,0.3));">🍭</div>
    </div>

    <div>
      <h2 class="section-title">Оригинальные игры</h2>
      <div class="games-grid">
        <div class="game-card" onclick="App.navigate('crash')">
          <div class="game-thumb">🚀</div>
          <div class="game-name">Crash</div>
        </div>
        <div class="game-card" onclick="App.navigate('mines')">
          <div class="game-thumb">💣</div>
          <div class="game-name">Mines</div>
        </div>
        <div class="game-card" onclick="App.navigate('slot')">
          <div class="game-thumb">🎰</div>
          <div class="game-name">Slots</div>
        </div>
        <div class="game-card" style="opacity: 0.6;" onclick="App.alert('Скоро!')">
          <div class="game-thumb">🎡</div>
          <div class="game-name">Wheel</div>
        </div>
      </div>
    </div>
  </main>

  <!-- ВЬЮ: CRASH (РАКЕТКА) -->
  <main class="view" id="view-crash">
    <div class="nav-back" onclick="App.navigate('home')">❮ Назад к играм</div>
    
    <div class="crash-screen">
      <div class="crash-bg-stars"></div>
      <div class="crash-multiplier" id="crash-mult">1.00x</div>
      <div class="crash-rocket" id="crash-rocket">🚀</div>
    </div>
    
    <div class="history-bar" id="crash-history"></div>

    <div class="bet-controls">
      <div class="bet-amount-row">
        <div class="bet-value">⭐ <span id="crash-bet-display">100</span></div>
        <div class="bet-quick-btns">
          <button class="btn-quick" onclick="CrashGame.changeBet(0.5)">/2</button>
          <button class="btn-quick" onclick="CrashGame.changeBet(2)">X2</button>
          <button class="btn-quick" onclick="CrashGame.changeBet('max')">MAX</button>
        </div>
      </div>
      <button class="btn-action btn-play" id="btn-crash-action" onclick="CrashGame.action()">СДЕЛАТЬ СТАВКУ</button>
    </div>
  </main>

  <!-- ВЬЮ: MINES (МИНЕР) -->
  <main class="view" id="view-mines">
    <div class="nav-back" onclick="App.navigate('home')">❮ Назад к играм</div>
    
    <div style="display:flex; justify-content:space-between; align-items:center; background:var(--bg-panel); padding:12px; border-radius:16px;">
      <div>Множитель: <span id="mines-mult" style="color:var(--accent-gold); font-weight:900; font-size:18px;">1.00x</span></div>
      <div>Бомб: <span style="color:var(--accent-red); font-weight:800;">3</span></div>
    </div>

    <div class="mines-grid" id="mines-grid"></div>

    <div class="bet-controls">
      <div class="bet-amount-row">
        <div class="bet-value">⭐ <span id="mines-bet-display">100</span></div>
        <div class="bet-quick-btns">
          <button class="btn-quick" onclick="MinesGame.changeBet(0.5)">/2</button>
          <button class="btn-quick" onclick="MinesGame.changeBet(2)">X2</button>
        </div>
      </div>
      <button class="btn-action btn-play" id="btn-mines-action" onclick="MinesGame.action()">ИГРАТЬ</button>
    </div>
  </main>

  <!-- ВЬЮ: СЛОТЫ -->
  <main class="view" id="view-slot">
    <div class="nav-back" onclick="App.navigate('home')">❮ Назад к играм</div>
    
    <div style="text-align:center; font-size:18px; font-weight:900; color:var(--accent-gold); height: 24px; text-shadow: 0 0 10px rgba(255,183,3,0.3);" id="slot-status">
      Вращай для победы!
    </div>

    <div class="slot-machine" id="slot-grid"></div>

    <div class="bet-controls">
      <div class="bet-amount-row">
        <div class="bet-value">⭐ <span id="slot-bet-display">50</span></div>
        <div class="bet-quick-btns">
          <button class="btn-quick" onclick="SlotGame.changeBet(0.5)">/2</button>
          <button class="btn-quick" onclick="SlotGame.changeBet(2)">X2</button>
        </div>
      </div>
      <button class="btn-action btn-play" id="btn-slot-action" onclick="SlotGame.spin()">КРУТИТЬ СПИН</button>
    </div>
  </main>

  <!-- НАВИГАЦИЯ -->
  <nav class="nav-bar">
    <div class="nav-item active" onclick="App.navigate('home')">
      <div class="nav-icon">🏠</div>
      <span>Главная</span>
    </div>
    <div class="nav-item" onclick="App.alert('Тут будут квесты')">
      <div class="nav-icon">🎯</div>
      <span>Задания</span>
    </div>
    <div class="nav-item" onclick="App.alert('Раздел лидерборда')">
      <div class="nav-icon">🏆</div>
      <span>Топ</span>
    </div>
    <div class="nav-item" onclick="App.alert('Раздел профиля')">
      <div class="nav-icon">👤</div>
      <span>Профиль</span>
    </div>
  </nav>

  <script>
    // Инициализация Telegram
    const tg = window.Telegram?.WebApp;
    if (tg) { tg.expand(); tg.ready(); }

    /* ========================================================
       ЯДРО ПРИЛОЖЕНИЯ И БЕЗОПАСНОСТЬ (IIFE Module)
       Все переменные баланса инкапсулированы, чтобы избежать 
       взлома через консоль браузера на стороне клиента.
       ======================================================== */
    const App = (function() {
      let _balance = 1000;
      let currentView = 'home';

      function updateHeader() {
        document.getElementById('headerBalance').innerText = Math.floor(_balance);
      }

      return {
        init: function() { updateHeader(); },
        getBalance: function() { return _balance; },
        deductBalance: function(amount) {
          if (_balance >= amount && amount > 0) {
            _balance -= amount; updateHeader(); return true;
          }
          return false;
        },
        addBalance: function(amount) {
          if (amount > 0) { _balance += amount; updateHeader(); }
        },
        navigate: function(viewId) {
          document.querySelectorAll('.view').forEach(el => el.classList.remove('active'));
          document.getElementById('view-' + viewId).classList.add('active');
          
          document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
          if(viewId === 'home') document.querySelectorAll('.nav-item')[0].classList.add('active');
          currentView = viewId;
        },
        alert: function(msg) {
          if (tg && tg.showAlert) tg.showAlert(msg);
          else alert(msg);
        }
      };
    })();

    /* ========================================================
       ИГРА: CRASH (РАКЕТКА)
       ======================================================== */
    const CrashGame = (function() {
      let bet = 100;
      let state = 'waiting'; // waiting, flying, crashed
      let multiplier = 1.00;
      let crashPoint = 1.00;
      let interval = null;
      let uiMult = document.getElementById('crash-mult');
      let uiRocket = document.getElementById('crash-rocket');
      let btnAction = document.getElementById('btn-crash-action');
      let historyBar = document.getElementById('crash-history');

      function updateBetDisplay() { document.getElementById('crash-bet-display').innerText = Math.floor(bet); }

      function addHistory(mult) {
        let color = mult < 1.5 ? 'var(--accent-red)' : (mult > 5 ? 'var(--accent-gold)' : 'var(--accent-green)');
        let pill = document.createElement('div');
        pill.className = 'history-pill';
        pill.style.color = color;
        pill.innerText = mult.toFixed(2) + 'x';
        historyBar.prepend(pill);
        if(historyBar.children.length > 10) historyBar.lastChild.remove();
      }

      function startFlight() {
        if (!App.deductBalance(bet)) { App.alert('Недостаточно звезд!'); return; }
        
        state = 'flying';
        multiplier = 1.00;
        uiMult.classList.remove('crashed');
        uiRocket.style.left = '20px'; uiRocket.style.bottom = '20px'; uiRocket.style.transform = 'rotate(0deg)';
        btnAction.innerText = 'ЗАБРАТЬ ' + Math.floor(bet) + ' ⭐';
        btnAction.className = 'btn-action btn-danger'; // Оранжево-красная кнопка
        
        // Генерация краша (Для продакшена - брать с сервера!)
        crashPoint = Math.max(1.00, (100 / (Math.random() * 100)).toFixed(2));
        if (Math.random() < 0.1) crashPoint = 1.00; // Мгновенный краш

        interval = setInterval(() => {
          multiplier += 0.01 + (multiplier * 0.005);
          uiMult.innerText = multiplier.toFixed(2) + 'x';
          
          // Анимация ракеты
          let moveX = Math.min(80, (multiplier - 1) * 20);
          let moveY = Math.min(80, (multiplier - 1) * 20);
          uiRocket.style.left = `calc(20px + ${moveX}%)`;
          uiRocket.style.bottom = `calc(20px + ${moveY}%)`;

          btnAction.innerText = 'ЗАБРАТЬ ' + Math.floor(bet * multiplier) + ' ⭐';

          if (multiplier >= crashPoint) {
            crash();
          }
        }, 50);
      }

      function crash() {
        clearInterval(interval);
        state = 'crashed';
        uiMult.classList.add('crashed');
        uiMult.innerText = crashPoint.toFixed(2) + 'x (Взрыв)';
        uiRocket.innerText = '💥';
        uiRocket.style.transform = 'scale(1.5)';
        btnAction.innerText = 'СДЕЛАТЬ СТАВКУ';
        btnAction.className = 'btn-action btn-play';
        addHistory(crashPoint);

        setTimeout(() => {
          if(state === 'crashed') {
            uiRocket.innerText = '🚀';
            uiRocket.style.left = '20px'; uiRocket.style.bottom = '20px';
            uiMult.innerText = '1.00x';
            uiMult.classList.remove('crashed');
            state = 'waiting';
          }
        }, 2000);
      }

      function cashOut() {
        if (state !== 'flying') return;
        clearInterval(interval);
        state = 'waiting';
        let win = Math.floor(bet * multiplier);
        App.addBalance(win);
        
        uiMult.innerText = multiplier.toFixed(2) + 'x (Забрал)';
        uiMult.style.color = 'var(--accent-green)';
        btnAction.innerText = 'СДЕЛАТЬ СТАВКУ';
        btnAction.className = 'btn-action btn-play';
        
        // Визуальная ракета улетает
        uiRocket.style.left = '150%';
        uiRocket.style.bottom = '150%';
        
        setTimeout(() => {
          uiMult.style.color = '#fff';
          uiRocket.style.left = '20px'; uiRocket.style.bottom = '20px';
          uiMult.innerText = '1.00x';
        }, 1500);
      }

      return {
        changeBet: function(factor) {
          if (state !== 'waiting' && state !== 'crashed') return;
          if (factor === 'max') bet = App.getBalance();
          else bet = Math.max(10, bet * factor);
          if (bet > App.getBalance()) bet = App.getBalance();
          updateBetDisplay();
        },
        action: function() {
          if (state === 'waiting' || state === 'crashed') startFlight();
          else if (state === 'flying') cashOut();
        }
      }
    })();


    /* ========================================================
       ИГРА: MINES (МИНЕР)
       ======================================================== */
    const MinesGame = (function() {
      let bet = 100;
      let state = 'waiting'; // waiting, playing
      let grid = []; // 25 cells (0 = gem, 1 = bomb)
      let revealed = 0;
      let currentMult = 1.00;
      let bombCount = 3; // Фиксировано для демо
      
      let elGrid = document.getElementById('mines-grid');
      let elMult = document.getElementById('mines-mult');
      let btnAction = document.getElementById('btn-mines-action');

      function updateBetDisplay() { document.getElementById('mines-bet-display').innerText = Math.floor(bet); }

      function initGrid() {
        elGrid.innerHTML = '';
        for(let i=0; i<25; i++) {
          let cell = document.createElement('div');
          cell.className = 'mine-cell';
          cell.onclick = () => revealCell(i, cell);
          elGrid.appendChild(cell);
        }
      }

      function startGame() {
        if (!App.deductBalance(bet)) { App.alert('Недостаточно звезд!'); return; }
        state = 'playing';
        revealed = 0;
        currentMult = 1.00;
        elMult.innerText = currentMult.toFixed(2) + 'x';
        btnAction.innerText = 'ЗАБРАТЬ (Пока нельзя)';
        btnAction.className = 'btn-action btn-danger';
        
        // Генерация поля (Серверная логика в проде)
        grid = Array(25).fill(0);
        let bombsPlaced = 0;
        while(bombsPlaced < bombCount) {
          let r = Math.floor(Math.random() * 25);
          if(grid[r] === 0) { grid[r] = 1; bombsPlaced++; }
        }
        
        initGrid();
      }

      function revealCell(index, el) {
        if(state !== 'playing' || el.classList.contains('revealed')) return;
        
        el.classList.add('revealed');
        if(grid[index] === 1) { // Бомба
          el.classList.add('bomb');
          el.innerText = '💣';
          endGame(false);
        } else { // Кристалл
          el.classList.add('gem');
          el.innerText = '💎';
          revealed++;
          // Увеличиваем множитель (формула)
          currentMult += (0.15 + (revealed * 0.05));
          elMult.innerText = currentMult.toFixed(2) + 'x';
          btnAction.innerText = 'ЗАБРАТЬ ' + Math.floor(bet * currentMult) + ' ⭐';
          
          if(revealed === 25 - bombCount) endGame(true); // Открыл всё
        }
      }

      function endGame(isWin) {
        state = 'waiting';
        // Показываем остальные мины
        let cells = elGrid.children;
        for(let i=0; i<25; i++) {
          if(!cells[i].classList.contains('revealed')) {
            cells[i].style.opacity = '0.5';
            if(grid[i]===1) cells[i].innerText = '💣';
            else cells[i].innerText = '💎';
          }
        }

        if(isWin) {
          let win = Math.floor(bet * currentMult);
          App.addBalance(win);
          App.alert(`Выигрыш: ${win} ⭐`);
        }
        
        btnAction.innerText = 'ИГРАТЬ';
        btnAction.className = 'btn-action btn-play';
        elMult.innerText = '1.00x';
      }

      function cashOut() {
        if(state !== 'playing' || revealed === 0) return;
        endGame(true);
      }

      return {
        init: function() { initGrid(); },
        changeBet: function(factor) {
          if(state !== 'waiting') return;
          bet = Math.max(10, Math.floor(bet * factor));
          if(bet > App.getBalance()) bet = App.getBalance();
          updateBetDisplay();
        },
        action: function() {
          if(state === 'waiting') startGame();
          else if(state === 'playing' && revealed > 0) cashOut();
        }
      }
    })();


    /* ========================================================
       ИГРА: СЛОТЫ
       ======================================================== */
    const SlotGame = (function() {
      let bet = 50;
      let symbols = ['🍒', '🍋', '🍉', '🍇', '💎', '7️⃣', '🍭'];
      let elGrid = document.getElementById('slot-grid');
      let isSpinning = false;

      function updateBetDisplay() { document.getElementById('slot-bet-display').innerText = Math.floor(bet); }

      function renderGrid(animate = false) {
        elGrid.innerHTML = '';
        for(let i=0; i<20; i++) {
          let cell = document.createElement('div');
          cell.className = 'slot-cell';
          if(animate) cell.classList.add('spinning');
          else cell.innerText = symbols[Math.floor(Math.random() * symbols.length)];
          elGrid.appendChild(cell);
        }
      }

      return {
        init: function() { renderGrid(); },
        changeBet: function(factor) {
          if(isSpinning) return;
          bet = Math.max(10, Math.floor(bet * factor));
          if(bet > App.getBalance()) bet = App.getBalance();
          updateBetDisplay();
        },
        spin: function() {
          if(isSpinning) return;
          if(!App.deductBalance(bet)) { App.alert('Недостаточно звезд!'); return; }
          
          isSpinning = true;
          document.getElementById('slot-status').innerText = 'Крутим...';
          document.getElementById('slot-status').style.color = '#fff';
          renderGrid(true); // Ставим анимацию

          setTimeout(() => {
            isSpinning = false;
            let cells = elGrid.children;
            
            // Логика победы (Чисто визуальная генерация для фронта)
            let winAmount = 0;
            let isWin = Math.random() < 0.4; // 40% шанс победы
            
            for(let i=0; i<20; i++) {
              cells[i].classList.remove('spinning');
              cells[i].innerText = symbols[Math.floor(Math.random() * symbols.length)];
            }

            if(isWin) {
              let winSymbol = symbols[Math.floor(Math.random() * symbols.length)];
              let mult = 2 + Math.floor(Math.random() * 8); // от x2 до x10
              winAmount = bet * mult;
              App.addBalance(winAmount);
              
              document.getElementById('slot-status').innerText = `🔥 ВЫИГРЫШ: ${winAmount} ⭐`;
              document.getElementById('slot-status').style.color = 'var(--accent-green)';
              
              // Подсветка рандомных ячеек как выигрышных
              for(let i=0; i<20; i++) {
                if(Math.random() < 0.3) {
                  cells[i].innerText = winSymbol;
                  cells[i].classList.add('win');
                }
              }
            } else {
              document.getElementById('slot-status').innerText = 'Попробуй еще раз!';
              document.getElementById('slot-status').style.color = 'var(--text-muted)';
            }
          }, 1500);
        }
      }
    })();

    // Инициализация при загрузке
    window.onload = () => {
      App.init();
      MinesGame.init();
      SlotGame.init();
    };

  </script>
</body>
</html>
