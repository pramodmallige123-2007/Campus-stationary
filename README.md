# Campus-stationary
index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>CampusMart - Live Stock, Add/Delete Feed & Queue</title>
  <link rel="manifest" href="./manifest.json" />
  <meta name="theme-color" content="#1e1b4b" />
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <style>
    body { font-family: 'Inter', sans-serif; }
    .pulse-dot { animation: pulse 1.8s infinite; }
    @keyframes pulse {
      0%, 100% { opacity: 1; transform: scale(1); }
      50% { opacity: 0.4; transform: scale(1.25); }
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-900 min-h-screen pb-24">

  <!-- ================= SIMPLE 1-STEP MOBILE NUMBER LOGIN ================= -->
  <div id="authGateModal" class="fixed inset-0 z-40 bg-indigo-950/95 backdrop-blur-md flex items-center justify-center p-4">
    <div class="bg-white rounded-3xl max-w-sm w-full p-6 shadow-2xl space-y-5 text-center">
      <div class="w-14 h-14 rounded-2xl bg-indigo-600 text-white flex items-center justify-center text-2xl mx-auto shadow-md">
        📚
      </div>
      <div class="space-y-1">
        <h2 class="text-xl font-extrabold text-slate-900">College Stationery Live</h2>
        <p class="text-xs text-slate-500">Enter your 10-digit mobile number to check live stock, newly added books & queue status</p>
      </div>

      <div class="space-y-3 text-left">
        <div class="flex">
          <span class="inline-flex items-center px-3.5 rounded-l-xl border border-r-0 border-slate-300 bg-slate-100 text-slate-600 text-sm font-bold">+91</span>
          <input 
            id="authPhoneInput" 
            type="tel" 
            maxlength="10" 
            placeholder="Enter 10-digit mobile number" 
            onkeydown="if(event.key==='Enter') loginWithMobile()"
            class="w-full px-3.5 py-3 rounded-r-xl border border-slate-300 text-base font-mono font-bold focus:outline-none focus:ring-2 focus:ring-indigo-600" 
          />
        </div>
        <p id="loginErrorMsg" class="hidden text-xs text-rose-600 font-semibold text-center"></p>
        <button onclick="loginWithMobile()" class="w-full py-3 rounded-xl bg-indigo-600 hover:bg-indigo-700 active:scale-95 text-white font-bold text-sm shadow-md transition">
          Enter Shop →
        </button>
      </div>
    </div>
  </div>

  <!-- Top Header -->
  <header class="sticky top-0 z-30 bg-indigo-950 text-white shadow-md">
    <div class="max-w-5xl mx-auto px-4 py-3 flex flex-wrap items-center justify-between gap-3">
      <div class="flex items-center gap-2.5">
        <div class="w-9 h-9 rounded-lg bg-indigo-500 flex items-center justify-center font-bold text-lg">📚</div>
        <div>
          <div class="flex items-center gap-2">
            <h1 class="font-bold text-base leading-tight">CampusStationery</h1>
            <span class="inline-flex items-center gap-1 text-[10px] font-semibold px-2 py-0.5 rounded-full bg-emerald-500/20 text-emerald-300 border border-emerald-500/30">
              <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 pulse-dot"></span> Live Sync
            </span>
          </div>
          <p id="studentProfileBar" class="text-[11px] text-indigo-300">📱 Not signed in</p>
        </div>
      </div>

      <div class="flex items-center gap-2">
        <button id="installAppBtn" onclick="installPWA()" class="hidden px-3 py-1.5 rounded-lg bg-emerald-500 hover:bg-emerald-600 text-slate-950 font-bold text-xs shadow-sm transition items-center gap-1">
          📲 Install App
        </button>
        <button id="btnSwitchRole" onclick="handleRoleSwitchClick()" class="px-3 py-1.5 rounded-lg bg-indigo-900 border border-indigo-700 text-indigo-200 text-xs font-semibold">
          🔐 Shopkeeper Login
        </button>
        <button onclick="logoutStudent()" class="px-2.5 py-1.5 rounded-lg bg-indigo-900/60 text-indigo-300 text-xs">
          Change No.
        </button>
      </div>
    </div>
  </header>

  <main class="max-w-5xl mx-auto px-4 pt-5">

    <!-- ================= STUDENT VIEW ================= -->
    <div id="studentView" class="space-y-5">

      <!-- 1. REAL-TIME VISUAL QUEUE SYSTEM -->
      <section class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200 space-y-4">
        <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
          <div class="space-y-1">
            <div class="flex items-center gap-2">
              <span class="text-xs font-bold uppercase tracking-wider text-slate-400">Real-Time Counter Queue</span>
              <span id="crowdBadge" class="inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-full text-xs font-semibold bg-amber-100 text-amber-800">
                <span class="w-2 h-2 rounded-full bg-amber-500 pulse-dot"></span> Moderate Rush
              </span>
            </div>
            <h2 id="crowdHeadline" class="text-xl font-extrabold text-slate-800">~8 mins wait at the counter</h2>
            <p id="lastUpdatedText" class="text-xs text-slate-400">Live status synced with shop counter</p>
          </div>

          <div class="flex items-center gap-3 bg-slate-50 p-3.5 rounded-xl border border-slate-200">
            <div class="text-center px-3 border-r border-slate-200">
              <p class="text-[10px] uppercase font-bold text-slate-400">Now Serving</p>
              <p id="nowServingDisplay" class="text-2xl font-extrabold text-indigo-600">#A-14</p>
            </div>
            <div class="text-center px-3">
              <p class="text-[10px] uppercase font-bold text-slate-400">People Ahead</p>
              <p id="inQueueDisplay" class="text-2xl font-bold text-slate-800">6</p>
            </div>
            <button onclick="openTokenModal()" class="ml-1 bg-indigo-600 hover:bg-indigo-700 active:scale-95 text-white font-semibold text-xs px-4 py-3 rounded-xl shadow-sm transition">
              Get Token <span id="cartCountBadge" class="ml-1 bg-indigo-800 px-1.5 py-0.5 rounded text-[10px]">0</span>
            </button>
          </div>
        </div>

        <!-- Visual Live Token Line Strip -->
        <div class="bg-slate-50 rounded-xl p-3 border border-slate-200/80">
          <div class="flex items-center justify-between text-[11px] font-semibold text-slate-500 mb-2">
            <span>👀 LIVE LINE AT COUNTER RIGHT NOW:</span>
            <span id="queueAvgSpeed">Avg speed: ~1.5 min / student</span>
          </div>
          <div id="visualQueueStrip" class="flex items-center gap-2 overflow-x-auto pb-1 text-xs"></div>
        </div>

        <!-- Student's Own Token & Exact Position Tracker -->
        <div id="myTokenBanner" class="hidden pt-2 space-y-2"></div>
      </section>

      <!-- 2. LIVE SHOP UPDATES (WHAT WAS ADDED, RESTOCKED OR DELETED) -->
      <section class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200">
        <div class="flex items-center justify-between mb-2.5">
          <div class="flex items-center gap-2">
            <span class="text-sm">📢</span>
            <h3 class="text-xs font-extrabold uppercase tracking-wider text-slate-700">Live Shop Updates (Added, Restocked & Removed Items)</h3>
          </div>
          <span class="text-[11px] text-slate-400 font-medium">Real-time log</span>
        </div>
        <div id="studentActivityFeed" class="flex gap-2.5 overflow-x-auto pb-1 text-xs"></div>
      </section>

      <!-- 3. SEARCH & CATEGORY FILTERS -->
      <section class="space-y-3">
        <div class="flex flex-col sm:flex-row gap-3">
          <div class="relative flex-1">
            <input id="searchInput" type="text" oninput="renderInventory()" placeholder="Search books, newly added manuals, drafter, bluebooks..." class="w-full pl-10 pr-4 py-2.5 bg-white border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500" />
            <span class="absolute left-3.5 top-3 text-slate-400 text-sm">🔍</span>
          </div>
          <label class="inline-flex items-center gap-2 text-sm text-slate-600 bg-white px-3.5 py-2 rounded-xl border border-slate-200 cursor-pointer">
            <input id="inStockOnly" type="checkbox" onchange="renderInventory()" class="rounded text-indigo-600" />
            <span>In-Stock Only</span>
          </label>
        </div>

        <div class="flex gap-2 overflow-x-auto pb-1 text-xs font-medium">
          <button onclick="setCategory('All')" data-cat="All" class="cat-btn px-3.5 py-1.5 rounded-full bg-slate-900 text-white">All Items</button>
          <button onclick="setCategory('Lab & Records')" data-cat="Lab & Records" class="cat-btn px-3.5 py-1.5 rounded-full bg-white border border-slate-200 text-slate-600">Lab & Records</button>
          <button onclick="setCategory('Textbooks')" data-cat="Textbooks" class="cat-btn px-3.5 py-1.5 rounded-full bg-white border border-slate-200 text-slate-600">Textbooks</button>
          <button onclick="setCategory('Engineering Gear')" data-cat="Engineering Gear" class="cat-btn px-3.5 py-1.5 rounded-full bg-white border border-slate-200 text-slate-600">Engineering Gear</button>
          <button onclick="setCategory('Exam Essentials')" data-cat="Exam Essentials" class="cat-btn px-3.5 py-1.5 rounded-full bg-white border border-slate-200 text-slate-600">Exam Essentials</button>
        </div>
      </section>

      <!-- 4. INVENTORY GRID -->
      <section id="inventoryGrid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4"></section>
    </div>

    <!-- ================= SHOPKEEPER PANEL ================= -->
    <div id="adminView" class="hidden space-y-6">
      <div class="bg-amber-50 border border-amber-200 rounded-xl p-3 flex items-center justify-between text-xs text-amber-900">
        <span>🔓 <strong>Shopkeeper Dashboard Active</strong> • Any item you add or delete notifies students immediately.</span>
        <button onclick="switchView('student')" class="px-3 py-1.5 rounded-lg bg-amber-900 text-white font-semibold">Back to Student View</button>
      </div>

      <!-- 1. Queue & Crowd Controls -->
      <section class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200 grid grid-cols-1 md:grid-cols-2 gap-4">
        <div class="p-4 rounded-xl bg-slate-50 border border-slate-200 flex items-center justify-between">
          <div>
            <p class="text-xs text-slate-500 font-medium">Now Serving Token</p>
            <p id="adminServingToken" class="text-3xl font-extrabold text-indigo-600">#A-14</p>
            <p class="text-xs text-slate-500 mt-1">Students waiting: <strong id="adminQueueCount">6</strong></p>
          </div>
          <div class="flex flex-col gap-2">
            <button onclick="callNextToken()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-semibold text-sm px-4 py-2.5 rounded-xl shadow-sm">
              🔊 Call Next Token
            </button>
            <div class="flex gap-1.5">
              <button onclick="addWalkInQueue(1)" class="flex-1 bg-white border border-slate-300 text-slate-700 text-xs font-semibold px-2.5 py-1.5 rounded-lg">
                +1 Walk-in
              </button>
              <button onclick="addWalkInQueue(-1)" class="bg-white border border-slate-300 text-slate-700 text-xs font-semibold px-2.5 py-1.5 rounded-lg">
                -1
              </button>
            </div>
          </div>
        </div>

        <div class="p-4 rounded-xl bg-slate-50 border border-slate-200 flex flex-col justify-between">
          <p class="text-xs text-slate-500 font-medium mb-2">1-Tap Shop Crowd Level</p>
          <div class="grid grid-cols-3 gap-2">
            <button onclick="setCrowdStatus('low')" class="p-2.5 rounded-lg border border-emerald-300 bg-emerald-50 text-emerald-800 text-xs font-semibold">🟢 Quiet</button>
            <button onclick="setCrowdStatus('moderate')" class="p-2.5 rounded-lg border border-amber-300 bg-amber-50 text-amber-800 text-xs font-semibold">🟡 Moderate</button>
            <button onclick="setCrowdStatus('high')" class="p-2.5 rounded-lg border border-rose-300 bg-rose-50 text-rose-800 text-xs font-semibold">🔴 Crowded</button>
          </div>
        </div>
      </section>

      <!-- 2. Mobile Token Orders (1-Tap Handover) -->
      <section class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200 space-y-3">
        <h2 class="text-base font-bold text-slate-800">1. Mobile Token Queue (1-Tap Handover)</h2>
        <div id="adminOrdersList" class="space-y-2.5"></div>
      </section>

      <!-- 3. ADD NEW ITEM TO SHOP (With Instant Student Notification) -->
      <section class="bg-white rounded-2xl p-5 shadow-sm border border-indigo-200 space-y-4">
        <div>
          <h2 class="text-base font-bold text-slate-800">2. ➕ Add New Book or Stationery Item</h2>
          <p class="text-xs text-slate-500">Adding an item puts a "✨ NEW" badge on it and alerts all students in the Live Updates bar</p>
        </div>

        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-3">
          <input id="newItemName" type="text" placeholder="Item Name (e.g. CAD Drawing Kit)" class="sm:col-span-2 px-3 py-2.5 border border-slate-300 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-indigo-600" />
          <input id="newItemPrice" type="number" placeholder="Price ₹ (e.g. 220)" class="px-3 py-2.5 border border-slate-300 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-indigo-600" />
          <select id="newItemCategory" class="px-3 py-2.5 border border-slate-300 rounded-xl text-sm bg-white focus:outline-none focus:ring-2 focus:ring-indigo-600">
            <option value="Lab & Records">Lab & Records</option>
            <option value="Textbooks">Textbooks</option>
            <option value="Engineering Gear">Engineering Gear</option>
            <option value="Exam Essentials">Exam Essentials</option>
          </select>
          <div class="flex gap-2">
            <input id="newItemRack" type="text" placeholder="Rack (e.g. B2)" class="w-24 px-3 py-2.5 border border-slate-300 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-indigo-600" />
            <button onclick="addNewInventoryItem()" class="flex-1 bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-xs px-4 py-2.5 rounded-xl shadow-sm transition">
              + Add Item
            </button>
          </div>
        </div>
      </section>

      <!-- 4. MANAGE EXISTING STOCK OR DELETE ITEMS -->
      <section class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200">
        <div class="flex items-center justify-between mb-3">
          <div>
            <h2 class="text-base font-bold text-slate-800">3. Manage Stock or Delete Items</h2>
            <p class="text-xs text-slate-500">Toggle stock status or click 🗑️ to delete discontinued items</p>
          </div>
          <button onclick="resetDemoData()" class="text-xs text-rose-600 hover:underline font-semibold">Reset Default Inventory</button>
        </div>
        <div class="divide-y divide-slate-100" id="adminStockList"></div>
      </section>
    </div>

  </main>

  <!-- Simple Checkout & UPI Modal -->
  <div id="tokenModal" class="fixed inset-0 z-50 hidden bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-5 shadow-xl space-y-4">
      <div class="flex items-center justify-between border-b border-slate-100 pb-3">
        <div>
          <h3 class="font-bold text-base text-slate-800">Get Queue Token</h3>
          <p id="checkoutStudentInfo" class="text-xs text-indigo-600 font-semibold"></p>
        </div>
        <button onclick="closeTokenModal()" class="text-slate-400 font-bold">✕</button>
      </div>

      <div id="modalCartItems" class="space-y-2 max-h-40 overflow-y-auto text-sm bg-slate-50 p-3 rounded-xl border border-slate-200"></div>

      <div class="grid grid-cols-2 gap-2 text-xs font-semibold">
        <button id="payMethodUpi" onclick="setPaymentMethod('UPI')" class="py-2.5 px-3 rounded-xl border-2 border-indigo-600 bg-indigo-50 text-indigo-900">⚡ Pay via UPI QR</button>
        <button id="payMethodCash" onclick="setPaymentMethod('CASH')" class="py-2.5 px-3 rounded-xl border border-slate-200 bg-white text-slate-600">💵 Pay at Counter</button>
      </div>

      <div id="upiPaymentSection" class="bg-indigo-950 text-white p-4 rounded-2xl space-y-2 text-center">
        <p class="text-xs text-indigo-200">Scan to pay <strong id="upiAmountBadge" class="text-white">₹0</strong></p>
        <div class="bg-white p-2.5 rounded-xl inline-block">
          <img id="upiQrImage" src="" alt="UPI QR" class="w-32 h-32 mx-auto" />
        </div>
      </div>

      <div class="flex justify-end gap-2 pt-2">
        <button onclick="closeTokenModal()" class="px-4 py-2 rounded-xl text-xs font-semibold text-slate-600">Cancel</button>
        <button onclick="confirmTokenBooking()" class="px-5 py-2.5 rounded-xl text-xs font-semibold bg-indigo-600 text-white">Confirm Token</button>
      </div>
    </div>
  </div>

  <script>
    // =========================================================================
    // CONFIGURATION (EDIT YOUR FIREBASE URL, UPI ID & PIN HERE)
    // =========================================================================
    const FIREBASE_DB_URL = ""; // Paste your Firebase Realtime Database URL here
    const SHOP_UPI_ID = "campusstationery@upi";
    const STAFF_PIN = "9999";

    const syncChannel = ('BroadcastChannel' in window) ? new BroadcastChannel('stationery_live_v4') : null;

    const defaultState = {
      currentTokenNumber: 14,
      queueLength: 4,
      crowdLevel: 'moderate',
      lastUpdated: "Just now",
      inventory: [
        { id: 1, name: "B.Tech Engineering Physics Lab Manual", category: "Lab & Records", price: 140, status: "in_stock", rack: "Rack A1", isNew: false },
        { id: 2, name: "Hardbound Lab Record Book (160 Pages)", category: "Lab & Records", price: 110, status: "low_stock", rack: "Rack A2", isNew: false },
        { id: 3, name: "Mini Drafter + A2 Drawing Sheet Pack", category: "Engineering Gear", price: 650, status: "in_stock", rack: "Rack C1", isNew: false },
        { id: 4, name: "University Blue Book / Assignment Booklet", category: "Exam Essentials", price: 15, status: "in_stock", rack: "Counter", isNew: false },
        { id: 5, name: "Casio fx-991CW Scientific Calculator", category: "Engineering Gear", price: 1350, status: "out_of_stock", rack: "Glass Case", isNew: false },
        { id: 6, name: "Engineering Mathematics-I (BS Grewal)", category: "Textbooks", price: 580, status: "low_stock", rack: "Rack B3", isNew: false },
        { id: 7, name: "C Programming & Data Structures Notes", category: "Textbooks", price: 180, status: "in_stock", rack: "Rack B1", isNew: true }
      ],
      activityLog: [
        { type: "ADDED", text: "✨ NEW ADDED: C Programming & Data Structures Notes (₹180)", time: "10m ago" },
        { type: "RESTOCK", text: "📦 RESTOCKED: University Blue Books (Counter)", time: "25m ago" },
        { type: "DELETED", text: "🗑️ REMOVED: 2024 Old Syllabus Chemistry Manual", time: "1h ago" }
      ],
      orders: [
        { token: "#A-15", phone: "9876543210", items: ["1x Mini Drafter Pack"], total: 650, paymentMethod: "UPI", status: "WAITING" }
      ]
    };

    let state = JSON.parse(localStorage.getItem('stationery_state_v4') || JSON.stringify(defaultState));
    let studentPhone = localStorage.getItem('student_mobile_no') || null;
    let currentView = 'student';
    let selectedCategory = 'All';
    let cart = {};
    let selectedPaymentMethod = 'UPI';

    // =========================================================================
    // 1. SIMPLE MOBILE NUMBER LOGIN
    // =========================================================================
    function checkAuthGate() {
      const gate = document.getElementById('authGateModal');
      const bar = document.getElementById('studentProfileBar');
      if (studentPhone) {
        gate.classList.add('hidden');
        bar.textContent = `📱 Student Mobile: +91 ${studentPhone}`;
      } else {
        gate.classList.remove('hidden');
        bar.textContent = `📱 Not signed in`;
      }
    }

    function loginWithMobile() {
      const phone = document.getElementById('authPhoneInput').value.trim();
      const err = document.getElementById('loginErrorMsg');
      if (!/^[6-9]\d{9}$/.test(phone)) {
        err.textContent = "Please enter a valid 10-digit mobile number.";
        err.classList.remove('hidden');
        return;
      }
      err.classList.add('hidden');
      studentPhone = phone;
      localStorage.setItem('student_mobile_no', phone);
      checkAuthGate();
      renderAll();
    }

    function logoutStudent() {
      localStorage.removeItem('student_mobile_no');
      studentPhone = null;
      document.getElementById('authPhoneInput').value = '';
      checkAuthGate();
    }

    // =========================================================================
    // 2. REAL-TIME SYNC ENGINE (CROSS-TAB + FIREBASE)
    // =========================================================================
    function playChime() {
      try {
        const ctx = new (window.AudioContext || window.webkitAudioContext)();
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.frequency.value = 880;
        gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + 0.4);
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.start();
        osc.stop(ctx.currentTime + 0.4);
      } catch (e) {}
      if ('vibrate' in navigator) navigator.vibrate([250, 100, 250]);
    }

    function getTimeStr() {
      return new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
    }

    function addActivityLog(type, text) {
      if (!state.activityLog) state.activityLog = [];
      state.activityLog.unshift({ type, text, time: getTimeStr() });
      if (state.activityLog.length > 10) state.activityLog.pop();
    }

    async function pushStateUpdate() {
      state.lastUpdated = getTimeStr();
      localStorage.setItem('stationery_state_v4', JSON.stringify(state));
      if (syncChannel) syncChannel.postMessage(state);
      renderAll();

      if (FIREBASE_DB_URL) {
        try {
          await fetch(`${FIREBASE_DB_URL}/shopLiveV4.json`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(state)
          });
        } catch (e) {}
      }
    }

    if (syncChannel) {
      syncChannel.onmessage = (e) => {
        if (e.data) { state = e.data; renderAll(); }
      };
    }

    window.addEventListener('storage', (e) => {
      if (e.key === 'stationery_state_v4' && e.newValue) {
        state = JSON.parse(e.newValue);
        renderAll();
      }
    });

    if (FIREBASE_DB_URL) {
      setInterval(async () => {
        try {
          const res = await fetch(`${FIREBASE_DB_URL}/shopLiveV4.json`);
          const cloud = await res.json();
          if (cloud) { state = cloud; renderAll(); }
        } catch (e) {}
      }, 3000);
    }

    function resetDemoData() {
      state = JSON.parse(JSON.stringify(defaultState));
      pushStateUpdate();
    }

    // =========================================================================
    // 3. SHOPKEEPER ITEM ADD, DELETE & RESTOCK FUNCTIONS
    // =========================================================================
    function addNewInventoryItem() {
      const name = document.getElementById('newItemName').value.trim();
      const price = Number(document.getElementById('newItemPrice').value);
      const category = document.getElementById('newItemCategory').value;
      const rack = document.getElementById('newItemRack').value.trim() || "Counter";

      if (!name || !price || price <= 0) {
        alert("Please enter both an Item Name and a valid Price (₹).");
        return;
      }

      const newId = state.inventory.length > 0 ? Math.max(...state.inventory.map(i => i.id)) + 1 : 1;
      const newItem = {
        id: newId,
        name,
        category,
        price,
        status: "in_stock",
        rack,
        isNew: true
      };

      state.inventory.unshift(newItem);
      addActivityLog("ADDED", `✨ NEW ADDED: ${name} (₹${price}) in ${category}`);

      document.getElementById('newItemName').value = '';
      document.getElementById('newItemPrice').value = '';
      document.getElementById('newItemRack').value = '';

      pushStateUpdate();
    }

    function deleteInventoryItem(id) {
      const item = state.inventory.find(i => i.id === id);
      if (!item) return;

      state.inventory = state.inventory.filter(i => i.id !== id);
      delete cart[id];
      addActivityLog("DELETED", `🗑️ REMOVED: "${item.name}" is no longer available`);
      pushStateUpdate();
    }

    function updateItemStatus(id, newStatus) {
      const item = state.inventory.find(i => i.id === id);
      if (!item) return;

      const oldStatus = item.status;
      item.status = newStatus;

      if (oldStatus === 'out_of_stock' && newStatus === 'in_stock') {
        addActivityLog("RESTOCK", `📦 RESTOCKED: "${item.name}" is back In Stock!`);
      } else if (newStatus === 'out_of_stock') {
        addActivityLog("OUT", `⚠️ SOLD OUT: "${item.name}" just went Out of Stock`);
      }

      pushStateUpdate();
    }

    // =========================================================================
    // 4. REAL-TIME VISUAL QUEUE SYSTEM
    // =========================================================================
    function handleRoleSwitchClick() {
      if (currentView === 'admin') {
        switchView('student');
      } else {
        const pin = prompt("Enter Shopkeeper PIN (Demo: 9999):");
        if (pin === STAFF_PIN) switchView('admin');
        else if (pin !== null) alert("Wrong PIN!");
      }
    }

    function switchView(view) {
      currentView = view;
      document.getElementById('studentView').classList.toggle('hidden', view !== 'student');
      document.getElementById('adminView').classList.toggle('hidden', view !== 'admin');
      document.getElementById('btnSwitchRole').textContent = view === 'admin' ? '🎓 Student View' : '🔐 Shopkeeper Login';
      renderAll();
    }

    function setCrowdStatus(level) {
      state.crowdLevel = level;
      state.queueLength = level === 'low' ? 1 : level === 'moderate' ? 5 : 14;
      pushStateUpdate();
    }

    function callNextToken() {
      state.currentTokenNumber++;
      if (state.queueLength > 0) state.queueLength--;
      if (state.queueLength <= 2) state.crowdLevel = 'low';
      else if (state.queueLength <= 7) state.crowdLevel = 'moderate';
      playChime();
      pushStateUpdate();
    }

    function addWalkInQueue(delta) {
      state.queueLength = Math.max(0, state.queueLength + delta);
      if (state.queueLength > 10) state.crowdLevel = 'high';
      else if (state.queueLength > 3) state.crowdLevel = 'moderate';
      else state.crowdLevel = 'low';
      pushStateUpdate();
    }

    function renderQueueBanner() {
      const badge = document.getElementById('crowdBadge');
      const headline = document.getElementById('crowdHeadline');
      const estWaitMins = Math.max(1, Math.round(state.queueLength * 1.5));

      if (state.crowdLevel === 'low' || state.queueLength <= 2) {
        badge.className = "inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-full text-xs font-semibold bg-emerald-100 text-emerald-800";
        badge.innerHTML = `<span class="w-2 h-2 rounded-full bg-emerald-500 pulse-dot"></span> Quiet • Walk Right In`;
        headline.textContent = state.queueLength === 0 ? "Counter is 100% free right now!" : `Only ${state.queueLength} person ahead (~2 mins)`;
      } else if (state.crowdLevel === 'moderate') {
        badge.className = "inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-full text-xs font-semibold bg-amber-100 text-amber-800";
        badge.innerHTML = `<span class="w-2 h-2 rounded-full bg-amber-500 pulse-dot"></span> Moderate Queue`;
        headline.textContent = `~${estWaitMins} mins wait (${state.queueLength} students in line)`;
      } else {
        badge.className = "inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-full text-xs font-semibold bg-rose-100 text-rose-800";
        badge.innerHTML = `<span class="w-2 h-2 rounded-full bg-rose-500 pulse-dot"></span> Heavy Rush`;
        headline.textContent = `~${estWaitMins}+ mins wait • Book a token online`;
      }

      document.getElementById('lastUpdatedText').textContent = `Last counter update: ${state.lastUpdated || 'Just now'}`;

      const servingStr = `#A-${state.currentTokenNumber}`;
      document.getElementById('nowServingDisplay').textContent = servingStr;
      document.getElementById('inQueueDisplay').textContent = state.queueLength;
      document.getElementById('adminServingToken').textContent = servingStr;
      document.getElementById('adminQueueCount').textContent = state.queueLength;

      const strip = document.getElementById('visualQueueStrip');
      const myOrders = (state.orders || []).filter(o => o.phone === studentPhone && o.status !== 'DONE');
      const myTokenSet = new Set(myOrders.map(o => o.token));

      let pillsHtml = `
        <div class="px-3 py-1.5 rounded-lg bg-indigo-600 text-white font-bold shrink-0 flex items-center gap-1.5 shadow-xs">
          <span>🔊 Serving ${servingStr}</span>
        </div>
      `;

      for (let i = 1; i <= Math.min(state.queueLength, 8); i++) {
        const nextTok = `#A-${state.currentTokenNumber + i}`;
        const isMine = myTokenSet.has(nextTok);
        pillsHtml += `
          <span class="text-slate-300 font-bold">→</span>
          <div class="px-2.5 py-1 rounded-lg font-mono font-bold shrink-0 border ${isMine ? 'bg-emerald-600 text-white border-emerald-700 animate-pulse' : 'bg-white text-slate-700 border-slate-200'}">
            ${nextTok} ${isMine ? '(YOU)' : ''}
          </div>
        `;
      }
      if (state.queueLength > 8) {
        pillsHtml += `<span class="text-xs text-slate-400 font-semibold shrink-0">+${state.queueLength - 8} more</span>`;
      }
      strip.innerHTML = pillsHtml;

      const myBanner = document.getElementById('myTokenBanner');
      if (myOrders.length > 0) {
        myBanner.classList.remove('hidden');
        myBanner.innerHTML = myOrders.map(o => {
          const tokNum = parseInt(o.token.replace('#A-', ''), 10) || state.currentTokenNumber;
          const posInLine = Math.max(0, tokNum - state.currentTokenNumber);
          const isReady = o.status === 'READY' || posInLine === 0;

          return `
            <div class="p-3.5 rounded-xl border ${isReady ? 'bg-emerald-50 border-emerald-300' : 'bg-indigo-50 border-indigo-200'} flex flex-wrap items-center justify-between gap-2">
              <div>
                <div class="flex items-center gap-2">
                  <span class="text-sm font-extrabold text-indigo-950">Your Token: ${o.token}</span>
                  <span class="px-2.5 py-0.5 rounded-full text-xs font-bold ${isReady ? 'bg-emerald-600 text-white' : 'bg-indigo-600 text-white'}">
                    ${isReady ? '🎉 IT IS YOUR TURN! Collect at Counter' : `⏳ Position #${posInLine} in line (~${Math.max(1, Math.round(posInLine * 1.5))}m wait)`}
                  </span>
                </div>
                <p class="text-xs text-slate-600 mt-1">${o.items.join(', ')} • <strong>₹${o.total} (${o.paymentMethod})</strong></p>
              </div>
              <span class="font-mono text-xs font-bold text-slate-500">📱 ${o.phone}</span>
            </div>
          `;
        }).join('');
      } else {
        myBanner.classList.add('hidden');
      }
    }

    // =========================================================================
    // 5. RENDER LIVE SHOP ACTIVITY FEED
    // =========================================================================
    function renderActivityFeed() {
      const feed = document.getElementById('studentActivityFeed');
      const logs = state.activityLog || [];
      if (logs.length === 0) {
        feed.innerHTML = `<span class="text-slate-400">No recent catalog changes today.</span>`;
        return;
      }

      feed.innerHTML = logs.map(log => {
        let style = 'bg-slate-50 border-slate-200 text-slate-700';
        if (log.type === 'ADDED') style = 'bg-indigo-50 border-indigo-200 text-indigo-900';
        if (log.type === 'RESTOCK') style = 'bg-emerald-50 border-emerald-200 text-emerald-900';
        if (log.type === 'DELETED' || log.type === 'OUT') style = 'bg-rose-50 border-rose-200 text-rose-900';

        return `
          <div class="px-3 py-2 rounded-xl border ${style} shrink-0 flex items-center gap-2">
            <span class="font-semibold">${log.text}</span>
            <span class="text-[10px] opacity-60 font-mono">${log.time}</span>
          </div>
        `;
      }).join('');
    }

    // =========================================================================
    // 6. INVENTORY & CART
    // =========================================================================
    function setCategory(cat) {
      selectedCategory = cat;
      document.querySelectorAll('.cat-btn').forEach(btn => {
        btn.className = (btn.dataset.cat === cat)
          ? "cat-btn px-3.5 py-1.5 rounded-full bg-slate-900 text-white"
          : "cat-btn px-3.5 py-1.5 rounded-full bg-white border border-slate-200 text-slate-600";
      });
      renderInventory();
    }

    function changeQty(id, delta) {
      const next = Math.max(0, (cart[id] || 0) + delta);
      if (next === 0) delete cart[id];
      else cart[id] = next;
      document.getElementById('cartCountBadge').textContent = Object.values(cart).reduce((a, b) => a + b, 0);
      renderInventory();
    }

    function renderInventory() {
      const query = document.getElementById('searchInput').value.toLowerCase();
      const hideOut = document.getElementById('inStockOnly').checked;
      const grid = document.getElementById('inventoryGrid');

      const filtered = state.inventory.filter(item => {
        const matchesCat = selectedCategory === 'All' || item.category === selectedCategory;
        const matchesQuery = item.name.toLowerCase().includes(query) || item.category.toLowerCase().includes(query);
        const matchesStock = !hideOut || item.status !== 'out_of_stock';
        return matchesCat && matchesQuery && matchesStock;
      });

      grid.innerHTML = filtered.map(item => {
        const badge = item.status === 'in_stock'
          ? `<span class="px-2.5 py-0.5 rounded-full text-[11px] font-semibold bg-emerald-50 text-emerald-700 border border-emerald-200">In Stock</span>`
          : item.status === 'low_stock'
            ? `<span class="px-2.5 py-0.5 rounded-full text-[11px] font-semibold bg-amber-50 text-amber-700 border border-amber-200">Few Left</span>`
            : `<span class="px-2.5 py-0.5 rounded-full text-[11px] font-semibold bg-rose-50 text-rose-600 border border-rose-200">Out of Stock</span>`;

        const newPill = item.isNew
          ? `<span class="px-2 py-0.5 rounded-md text-[10px] font-extrabold bg-indigo-600 text-white">✨ NEW</span>`
          : '';

        const qty = cart[item.id] || 0;
        const btn = item.status === 'out_of_stock'
          ? `<button disabled class="w-full py-2 rounded-xl bg-slate-100 text-slate-400 text-xs font-semibold">Unavailable</button>`
          : qty === 0
            ? `<button onclick="changeQty(${item.id}, 1)" class="w-full py-2 rounded-xl bg-slate-900 text-white text-xs font-semibold">+ Add</button>`
            : `<div class="flex items-center justify-between bg-indigo-50 border border-indigo-200 rounded-xl px-2 py-1">
                 <button onclick="changeQty(${item.id}, -1)" class="w-7 h-7 rounded-lg bg-white font-bold">-</button>
                 <span class="text-xs font-extrabold">${qty}</span>
                 <button onclick="changeQty(${item.id}, 1)" class="w-7 h-7 rounded-lg bg-indigo-600 text-white font-bold">+</button>
               </div>`;

        return `
          <div class="bg-white rounded-2xl p-4 shadow-xs border ${item.isNew ? 'border-indigo-300 ring-1 ring-indigo-100' : 'border-slate-200'} flex flex-col justify-between gap-3">
            <div>
              <div class="flex items-center justify-between gap-2 mb-2">
                <div class="flex items-center gap-1.5">
                  ${newPill}
                  <span class="text-[11px] font-medium text-slate-400">${item.category} • ${item.rack}</span>
                </div>
                ${badge}
              </div>
              <h3 class="font-bold text-sm text-slate-800">${item.name}</h3>
            </div>
            <div class="pt-2 border-t border-slate-100 flex items-center justify-between gap-3">
              <span class="font-extrabold text-base">₹${item.price}</span>
              <div class="w-36">${btn}</div>
            </div>
          </div>
        `;
      }).join('');
    }

    // =========================================================================
    // 7. TOKEN BOOKING & SHOPKEEPER MANAGEMENT
    // =========================================================================
    function getCartDetails() {
      const lines = [];
      let total = 0;
      for (const [idStr, qty] of Object.entries(cart)) {
        const item = state.inventory.find(i => i.id === Number(idStr));
        if (item && qty > 0) {
          lines.push({ name: item.name, qty, subtotal: item.price * qty });
          total += item.price * qty;
        }
      }
      return { lines, total };
    }

    function setPaymentMethod(method) {
      selectedPaymentMethod = method;
      document.getElementById('upiPaymentSection').classList.toggle('hidden', method !== 'UPI');
    }

    function openTokenModal() {
      if (!studentPhone) { checkAuthGate(); return; }
      document.getElementById('checkoutStudentInfo').textContent = `Mobile: +91 ${studentPhone}`;
      const { lines, total } = getCartDetails();
      const container = document.getElementById('modalCartItems');

      container.innerHTML = lines.length === 0
        ? `<p class="text-xs text-slate-500">Booking a general counter queue token.</p>`
        : lines.map(l => `<div class="flex justify-between text-xs py-1"><span>${l.qty}x ${l.name}</span><strong>₹${l.subtotal}</strong></div>`).join('') +
          `<div class="flex justify-between pt-2 border-t font-bold text-sm"><span>Total</span><span>₹${total}</span></div>`;

      document.getElementById('upiAmountBadge').textContent = `₹${total}`;
      const upiUri = `upi://pay?pa=${SHOP_UPI_ID}&pn=Stationery&am=${total}&cu=INR`;
      document.getElementById('upiQrImage').src = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${encodeURIComponent(upiUri)}`;
      document.getElementById('tokenModal').classList.remove('hidden');
    }

    function closeTokenModal() {
      document.getElementById('tokenModal').classList.add('hidden');
    }

    function confirmTokenBooking() {
      const { lines, total } = getCartDetails();
      state.queueLength++;
      const tokenStr = `#A-${state.currentTokenNumber + state.queueLength}`;

      state.orders.unshift({
        token: tokenStr,
        phone: studentPhone,
        items: lines.length > 0 ? lines.map(l => `${l.qty}x ${l.name}`) : ["Counter Visit"],
        total: total,
        paymentMethod: selectedPaymentMethod,
        status: 'WAITING'
      });

      cart = {};
      document.getElementById('cartCountBadge').textContent = '0';
      closeTokenModal();
      pushStateUpdate();
    }

    function markOrderReady(token) {
      const o = state.orders.find(x => x.token === token);
      if (o) { o.status = 'READY'; playChime(); pushStateUpdate(); }
    }

    function completeOrder(token) {
      const o = state.orders.find(x => x.token === token);
      if (o) {
        o.status = 'DONE';
        if (state.queueLength > 0) state.queueLength--;
        pushStateUpdate();
      }
    }

    function renderAdminOrders() {
      const list = document.getElementById('adminOrdersList');
      const active = (state.orders || []).filter(o => o.status !== 'DONE');
      if (active.length === 0) {
        list.innerHTML = `<p class="text-xs text-slate-400 py-3 text-center">No active tokens in queue.</p>`;
        return;
      }
      list.innerHTML = active.map(o => `
        <div class="p-3.5 rounded-xl border border-slate-200 bg-slate-50 flex flex-wrap items-center justify-between gap-3">
          <div>
            <span class="font-extrabold text-indigo-700 text-base">${o.token}</span>
            <span class="ml-2 font-mono text-xs font-bold text-slate-800">📱 ${o.phone}</span>
            <span class="ml-2 text-xs px-2 py-0.5 rounded bg-slate-200 font-semibold">₹${o.total} (${o.paymentMethod})</span>
            <p class="text-xs text-slate-600 mt-1">${o.items.join(' • ')}</p>
          </div>
          <div class="flex gap-2">
            ${o.status === 'WAITING' ? `<button onclick="markOrderReady('${o.token}')" class="px-3 py-1.5 rounded-lg bg-amber-500 text-white text-xs font-semibold">🔔 Call Ready</button>` : ''}
            <button onclick="completeOrder('${o.token}')" class="px-3 py-1.5 rounded-lg bg-emerald-600 text-white text-xs font-semibold">✓ Handed Over</button>
          </div>
        </div>
      `).join('');
    }

    function renderAdminStock() {
      document.getElementById('adminStockList').innerHTML = state.inventory.map(item => `
        <div class="py-3 flex flex-wrap items-center justify-between gap-2 text-xs">
          <div>
            <span class="font-bold text-slate-800">${item.name}</span>
            <span class="text-slate-400 ml-1">(₹${item.price} • ${item.category} • ${item.rack})</span>
          </div>
          <div class="flex items-center gap-1.5">
            <button onclick="updateItemStatus(${item.id}, 'in_stock')" class="px-2.5 py-1 rounded-lg font-semibold border ${item.status === 'in_stock' ? 'bg-emerald-600 text-white border-emerald-600' : 'bg-white text-slate-600'}">In</button>
            <button onclick="updateItemStatus(${item.id}, 'low_stock')" class="px-2.5 py-1 rounded-lg font-semibold border ${item.status === 'low_stock' ? 'bg-amber-500 text-white border-amber-500' : 'bg-white text-slate-600'}">Low</button>
            <button onclick="updateItemStatus(${item.id}, 'out_of_stock')" class="px-2.5 py-1 rounded-lg font-semibold border ${item.status === 'out_of_stock' ? 'bg-rose-600 text-white border-rose-600' : 'bg-white text-slate-600'}">Out</button>
            <button onclick="deleteInventoryItem(${item.id})" title="Delete Item" class="ml-2 px-2.5 py-1 rounded-lg bg-rose-50 hover:bg-rose-100 text-rose-600 border border-rose-200 font-bold">
              🗑️ Delete
            </button>
          </div>
        </div>
      `).join('');
    }

    function renderAll() {
      renderQueueBanner();
      renderActivityFeed();
      renderInventory();
      renderAdminOrders();
      renderAdminStock();
    }

    // =========================================================================
    // 8. PWA SERVICE WORKER & ANDROID HOME SCREEN INSTALL
    // =========================================================================
    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => navigator.serviceWorker.register('./sw.js').catch(() => {}));
    }

    let deferredInstallPrompt = null;
    const installBtn = document.getElementById('installAppBtn');

    window.addEventListener('beforeinstallprompt', (e) => {
      e.preventDefault();
      deferredInstallPrompt = e;
      installBtn.classList.remove('hidden');
      installBtn.classList.add('inline-flex');
    });

    async function installPWA() {
      if (!deferredInstallPrompt) return;
      deferredInstallPrompt.prompt();
      const { outcome } = await deferredInstallPrompt.userChoice;
      if (outcome === 'accepted') installBtn.classList.add('hidden');
      deferredInstallPrompt = null;
    }

    window.addEventListener('appinstalled', () => {
      installBtn.classList.add('hidden');
      deferredInstallPrompt = null;
    });

    checkAuthGate();
    renderAll();
  </script>
</body>
</html>





poster.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Print Shop Poster - CampusStationery Live</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@500;600;700;800;900&display=swap" rel="stylesheet">
  <style>
    body { font-family: 'Inter', sans-serif; }
    @media print {
      .no-print { display: none !important; }
      body { background: white !important; padding: 0 !important; }
      #posterCard { box-shadow: none !important; border: 3px solid #1e1b4b !important; margin: 0 auto !important; }
    }
  </style>
</head>
<body class="bg-slate-200 min-h-screen py-8 px-4">

  <!-- Top Controls (Hidden Automatically When Printing) -->
  <div class="no-print max-w-2xl mx-auto mb-6 bg-white p-4 rounded-2xl shadow-md border border-slate-300 flex flex-col sm:flex-row items-center gap-3">
    <div class="flex-1 w-full">
      <label class="block text-xs font-bold text-slate-600 mb-1">Paste Your Live GitHub Pages Link Here:</label>
      <input 
        id="appUrlInput" 
        type="text" 
        value="https://yourusername.github.io/campus-stationery/" 
        oninput="updatePosterQr()"
        class="w-full px-3 py-2 border border-slate-300 rounded-xl text-sm font-mono focus:outline-none focus:ring-2 focus:ring-indigo-600"
      />
    </div>
    <button onclick="window.print()" class="w-full sm:w-auto mt-2 sm:mt-5 px-5 py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-sm rounded-xl shadow-sm transition">
      🖨️ Print / Save PDF
    </button>
  </div>

  <!-- A4 Printable Poster Card -->
  <div id="posterCard" class="max-w-2xl mx-auto bg-white rounded-3xl p-8 sm:p-10 shadow-2xl border-4 border-indigo-950 text-center space-y-6">
    
    <div class="inline-block bg-rose-600 text-white font-extrabold text-xs sm:text-sm uppercase tracking-widest px-4 py-1.5 rounded-full">
      ⚡ Skip The Counter Crowd!
    </div>

    <div class="space-y-2">
      <h1 class="text-3xl sm:text-5xl font-black text-indigo-950 tracking-tight leading-none">
        DON'T STAND IN LINE.
      </h1>
      <p class="text-base sm:text-lg font-semibold text-slate-600">
        Check Live Book Stock & Book Your Queue Token From Your Phone
      </p>
    </div>

    <div class="bg-indigo-950 text-white rounded-3xl p-6 sm:p-8 max-w-md mx-auto space-y-4 shadow-inner">
      <p class="text-xs font-bold uppercase tracking-widest text-indigo-300">Scan With Camera or Google Lens</p>
      <div class="bg-white p-4 rounded-2xl inline-block shadow-lg">
        <img 
          id="posterQrImg" 
          src="https://api.qrserver.com/v1/create-qr-code/?size=280x280&data=https%3A%2F%2Fyourusername.github.io%2Fcampus-stationery%2F" 
          alt="Scan App QR" 
          class="w-56 h-56 mx-auto"
        />
      </div>
      <p id="posterUrlDisplay" class="font-mono text-sm font-bold text-emerald-400 break-all">
        yourusername.github.io/campus-stationery
      </p>
    </div>

    <div class="grid grid-cols-3 gap-3 pt-2 text-left">
      <div class="bg-slate-50 p-4 rounded-2xl border border-slate-200">
        <span class="text-xs font-black text-indigo-600 uppercase">Step 1</span>
        <p class="font-bold text-sm text-slate-900 mt-1">Enter Mobile No.</p>
        <p class="text-xs text-slate-500 mt-0.5">No college ID, password, or OTP needed.</p>
      </div>
      <div class="bg-slate-50 p-4 rounded-2xl border border-slate-200">
        <span class="text-xs font-black text-indigo-600 uppercase">Step 2</span>
        <p class="font-bold text-sm text-slate-900 mt-1">Check Stock & Book</p>
        <p class="text-xs text-slate-500 mt-0.5">See live book availability & get a token.</p>
      </div>
      <div class="bg-slate-50 p-4 rounded-2xl border border-slate-200">
        <span class="text-xs font-black text-indigo-600 uppercase">Step 3</span>
        <p class="font-bold text-sm text-slate-900 mt-1">Direct Pickup</p>
        <p class="text-xs text-slate-500 mt-0.5">Walk in only when your token is called!</p>
      </div>
    </div>

    <div class="pt-4 border-t border-slate-200 flex items-center justify-between text-xs font-semibold text-slate-500">
      <span>📚 College Stationery Shop • Main Block</span>
      <span>📲 Tap "Install App" after scanning</span>
    </div>

  </div>

  <script>
    function updatePosterQr() {
      const rawUrl = document.getElementById('appUrlInput').value.trim() || "https://yourusername.github.io/campus-stationery/";
      document.getElementById('posterQrImg').src = `https://api.qrserver.com/v1/create-qr-code/?size=280x280&data=${encodeURIComponent(rawUrl)}`;
      document.getElementById('posterUrlDisplay').textContent = rawUrl.replace(/^https?:\/\//, '');
    }
  </script>
</body>
</html>




manifest.json
{
  "name": "CampusStationery Live Stock & Queue",
  "short_name": "CampusMart",
  "description": "Check college stationery stock, live counter queue, and book express UPI tokens.",
  "start_url": "./index.html",
  "scope": "./",
  "display": "standalone",
  "orientation": "portrait",
  "background_color": "#f8fafc",
  "theme_color": "#1e1b4b",
  "icons": [
    {
      "src": "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 192 192'%3E%3Crect width='192' height='192' rx='42' fill='%231e1b4b'/%3E%3Ctext x='50%25' y='56%25' dominant-baseline='middle' text-anchor='middle' font-size='104'%3E📚%3C/text%3E%3C/svg%3E",
      "sizes": "192x192",
      "type": "image/svg+xml",
      "purpose": "any maskable"
    },
    {
      "src": "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Crect width='512' height='512' rx='110' fill='%231e1b4b'/%3E%3Ctext x='50%25' y='56%25' dominant-baseline='middle' text-anchor='middle' font-size='280'%3E📚%3C/text%3E%3C/svg%3E",
      "sizes": "512x512",
      "type": "image/svg+xml",
      "purpose": "any maskable"
    }
  ]
}
