<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Food Delivery Department - PKC Live Sync Wheel</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- Canvas Confetti -->
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
  
  <!-- Firebase SDKs for Real-time Cloud Synchronization (Stable Compat) -->
  <script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-auth-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore-compat.js"></script>

  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            pkc: {
              navy: '#0f2b5c',
              blue: '#1e40af',
              lightBlue: '#3b82f6',
              gold: '#d97706',
              goldHover: '#b45309',
              yellow: '#f59e0b',
              accent: '#fef3c7'
            }
          },
          keyframes: {
            wiggle: {
              '0%, 100%': { transform: 'rotate(0deg)' },
              '25%': { transform: 'rotate(-12deg)' },
              '75%': { transform: 'rotate(12deg)' },
            },
            float: {
              '0%, 100%': { transform: 'translateY(0px)' },
              '50%': { transform: 'translateY(-8px)' },
            },
            pulseGlow: {
              '0%, 100%': { opacity: '0.4', filter: 'drop-shadow(0 0 8px #f59e0b)' },
              '50%': { opacity: '1', filter: 'drop-shadow(0 0 16px #f59e0b)' },
            }
          },
          animation: {
            wiggle: 'wiggle 0.15s ease-in-out infinite',
            float: 'float 3s ease-in-out infinite',
            pulseGlow: 'pulseGlow 1.5s ease-in-out infinite'
          }
        }
      }
    }
  </script>
  <style>
    /* Custom Scrollbar */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #f1f5f9;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb {
      background: #cbd5e1;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94a3b8;
    }
    
    .fun-bg {
      background-color: #f8fafc;
      background-image: radial-gradient(#cbd5e1 1.2px, transparent 1.2px), radial-gradient(#e2e8f0 1.2px, #f8fafc 1.2px);
      background-size: 48px 48px;
      background-position: 0 0, 24px 24px;
    }

    .clip-pointer {
      clip-path: polygon(100% 0, 0 0, 50% 100%);
    }

    .pointer-wiggle {
      animation: wiggle 0.1s ease-in-out;
    }
  </style>
</head>
<body class="fun-bg text-slate-800 min-h-screen flex flex-col font-sans antialiased selection:bg-pkc-yellow selection:text-pkc-navy relative overflow-x-hidden">

  <!-- Floating Background Emojis -->
  <div class="fixed inset-0 pointer-events-none z-0 overflow-hidden opacity-20 flex justify-between px-6 py-10 select-none">
    <div class="text-4xl animate-float" style="animation-delay: 0s;">🍕</div>
    <div class="text-4xl animate-float" style="animation-delay: 1s;">🍜</div>
    <div class="text-4xl animate-float" style="animation-delay: 0.5s;">🍱</div>
    <div class="text-4xl animate-float" style="animation-delay: 1.5s;">🧋</div>
    <div class="text-4xl animate-float" style="animation-delay: 2s;">🍔</div>
  </div>

  <!-- HEADER -->
  <header class="bg-gradient-to-r from-pkc-navy via-blue-950 to-pkc-navy text-white shadow-xl border-b-4 border-pkc-yellow relative z-10">
    <div class="max-w-7xl mx-auto px-4 py-4 sm:px-6 lg:px-8 flex flex-col md:flex-row items-center justify-between gap-4">
      
      <!-- Brand & Title -->
      <div class="flex items-center gap-3 text-center sm:text-left">
        <div class="w-14 h-14 rounded-2xl bg-gradient-to-br from-amber-400 to-pkc-yellow flex items-center justify-center text-pkc-navy text-3xl font-black shadow-lg border-2 border-white transform hover:rotate-6 transition-transform cursor-pointer">
          🍕
        </div>
        <div>
          <div class="flex items-center gap-2 justify-center sm:justify-start">
            <h1 class="text-2xl sm:text-3xl font-black tracking-tight text-white drop-shadow-md">
              Food Delivery Department
            </h1>
            <span id="syncIndicator" class="bg-amber-500 text-white text-[10px] font-black uppercase px-2.5 py-0.5 rounded-full shadow border border-amber-300 flex items-center gap-1">
              <i class="fa-solid fa-hard-drive text-[9px]"></i> Local Storage
            </span>
          </div>
          <p class="text-xs sm:text-sm text-amber-300 font-bold tracking-wider uppercase flex items-center gap-1 justify-center sm:justify-start mt-0.5">
            <i class="fa-solid fa-graduation-cap"></i> Pui Kiu College Staff Edition 🛵
          </p>
        </div>
      </div>

      <!-- Top Action Controls & Room Code -->
      <div class="flex items-center gap-2 flex-wrap justify-center">
        <!-- Live Sync Room Selector -->
        <div class="bg-blue-950/80 border border-amber-400/80 rounded-xl px-3 py-1.5 flex items-center gap-2 shadow-inner">
          <span class="text-[10px] font-black uppercase text-amber-300 flex items-center gap-1">
            <i class="fa-solid fa-users text-xs"></i> Room:
          </span>
          <input type="text" id="roomCodeInput" value="PKC-STAFF" class="w-24 bg-blue-900/90 text-white text-xs font-black px-2 py-1 rounded border border-blue-600 focus:outline-none focus:ring-1 focus:ring-amber-400 uppercase text-center" title="Shared room code across all colleagues">
          <button id="changeRoomBtn" class="text-amber-300 hover:text-white text-xs font-bold" title="Switch Sync Room">
            <i class="fa-solid fa-rotate"></i>
          </button>
        </div>

        <!-- Sound Toggle -->
        <button id="soundToggleBtn" class="bg-blue-900/80 hover:bg-blue-800 text-amber-300 font-bold text-xs sm:text-sm px-3 py-2 rounded-xl transition duration-200 border border-blue-600/60 shadow flex items-center gap-1.5" title="Toggle Sound FX">
          <i id="soundIcon" class="fa-solid fa-volume-high"></i> <span id="soundText">Sound ON</span>
        </button>

        <button id="preloadBtn" class="bg-gradient-to-r from-amber-500 to-pkc-yellow hover:from-amber-600 hover:to-amber-500 text-pkc-navy font-black text-xs sm:text-sm px-3.5 py-2 rounded-xl transition duration-200 shadow-md flex items-center gap-1.5 border border-amber-300 transform hover:-translate-y-0.5">
          <i class="fa-solid fa-map-location-dot"></i> Pre-load Spots
        </button>
        
        <button id="exportBtn" class="bg-blue-800/80 hover:bg-blue-700 text-white font-semibold text-xs sm:text-sm px-3 py-2 rounded-xl transition duration-200 flex items-center gap-1.5 border border-blue-600 shadow">
          <i class="fa-solid fa-file-export"></i> Export
        </button>
      </div>
    </div>

    <!-- PAGE NAVIGATION TABS -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 mt-2 flex gap-2 overflow-x-auto">
      <button id="navTabSpinner" class="nav-tab active-tab bg-white text-pkc-navy font-black text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl shadow-md border-t-2 border-x-2 border-white flex items-center gap-2 transition whitespace-nowrap">
        <i class="fa-solid fa-dharmachakra text-amber-500"></i> Lunch Spinner
      </button>
      <button id="navTabOrders" class="nav-tab bg-blue-900/60 hover:bg-blue-900 text-white font-bold text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl border-t border-x border-blue-700/60 flex items-center gap-2 transition whitespace-nowrap">
        <i class="fa-solid fa-clipboard-list text-amber-400"></i> Group Order Sheet
        <span id="orderBadgeCount" class="bg-amber-400 text-pkc-navy text-[10px] font-black px-2 py-0.2 rounded-full hidden">0</span>
      </button>
      <button id="navTabSplitter" class="nav-tab bg-blue-900/60 hover:bg-blue-900 text-white font-bold text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl border-t border-x border-blue-700/60 flex items-center gap-2 transition whitespace-nowrap">
        <i class="fa-solid fa-calculator text-emerald-400"></i> Payment Splitter
      </button>
    </div>
  </header>

  <!-- MAIN CONTENT CONTAINER -->
  <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-grow w-full relative z-10">
    
    <!-- PAGE 1: LUNCH SPINNER VIEW -->
    <div id="pageSpinner" class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start">
      
      <!-- LEFT COLUMN: Decision Wheel Display (7 cols) -->
      <section class="lg:col-span-7 flex flex-col items-center bg-white p-6 sm:p-8 rounded-3xl shadow-xl border-2 border-slate-200/80 relative overflow-hidden">
        <div class="w-full text-center mb-4">
          <div class="inline-flex items-center gap-2 bg-amber-100 text-pkc-navy text-xs font-black uppercase tracking-widest px-3 py-1 rounded-full mb-1 border border-amber-300">
            <i class="fa-solid fa-wand-magic-sparkles text-amber-600"></i> Hunger Relief Wheel
          </div>
          <h2 class="text-xl sm:text-2xl font-black text-pkc-navy tracking-tight">
            What's For Lunch Today?
          </h2>
          <p class="text-xs text-slate-500 mt-1">Spin to solve teacher lunchtime indecision once and for all!</p>
        </div>

        <!-- Wheel Canvas Container -->
        <div class="relative flex items-center justify-center my-3 max-w-full group">
          <div id="lightsRing" class="absolute inset-0 -m-3 rounded-full border-8 border-dashed border-amber-400 opacity-60 transition-all duration-300 pointer-events-none"></div>

          <div id="wheelPointer" class="absolute -top-4 z-20 flex flex-col items-center pointer-events-none drop-shadow-xl transform transition-transform origin-bottom">
            <div class="w-9 h-11 bg-red-600 clip-pointer"></div>
            <div class="w-5 h-5 bg-amber-400 rounded-full border-2 border-white -mt-3 shadow-inner"></div>
          </div>
          
          <canvas id="wheelCanvas" width="460" height="460" class="max-w-full h-auto rounded-full shadow-2xl border-8 border-pkc-navy bg-slate-50 cursor-pointer transform transition-transform active:scale-[0.99]"></canvas>
        </div>

        <!-- Spin Controls & Status -->
        <div class="mt-4 w-full max-w-md flex flex-col items-center gap-3">
          <button id="spinBtn" class="w-full bg-gradient-to-r from-pkc-gold via-amber-500 to-pkc-yellow hover:from-amber-600 hover:to-amber-500 text-pkc-navy font-black text-2xl py-4 px-8 rounded-2xl shadow-xl hover:shadow-2xl transform hover:-translate-y-1 active:translate-y-0.5 transition-all duration-150 border-4 border-amber-300 flex items-center justify-center gap-3 group">
            <i class="fa-solid fa-dice text-3xl group-hover:rotate-180 transition-transform duration-500"></i> SPIN THE WHEEL!
          </button>
          
          <div id="statusMessage" class="text-xs sm:text-sm font-bold text-slate-600 bg-slate-100 px-4 py-2 rounded-xl text-center w-full border border-slate-200/80 shadow-inner flex items-center justify-center gap-2">
            <span>🎯</span> <span id="statusText">Ready to select today's feast!</span>
          </div>
        </div>
      </section>

      <!-- RIGHT COLUMN: Restaurant Management (5 cols) -->
      <section class="lg:col-span-5 flex flex-col gap-6">
        
        <!-- Add New Restaurant Card -->
        <div class="bg-white p-5 sm:p-6 rounded-3xl shadow-xl border-2 border-slate-200/80">
          <h2 class="text-base font-black text-pkc-navy mb-3 flex items-center justify-between">
            <span class="flex items-center gap-2">
              <i class="fa-solid fa-plus-circle text-pkc-gold text-lg"></i> Add Lunch Spot
            </span>
            <span class="text-[10px] font-bold bg-blue-50 text-pkc-blue px-2 py-0.5 rounded-full border border-blue-200">Custom Option</span>
          </h2>
          
          <form id="addRestaurantForm" class="flex flex-col gap-3">
            <div>
              <label for="restName" class="block text-xs font-bold text-slate-600 uppercase mb-1">Restaurant Name *</label>
              <input type="text" id="restName" placeholder="e.g. Lung Wah Hotel Roast Pigeon" required class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-pkc-blue focus:border-transparent bg-slate-50/50">
            </div>
            
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
              <div>
                <label for="restCuisine" class="block text-xs font-bold text-slate-600 uppercase mb-1">Category</label>
                <select id="restCuisine" class="w-full px-3 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-pkc-blue focus:border-transparent bg-slate-50/50">
                  <option value="Hong Kong / Cafe">☕ HK / Cha Chaan Teng</option>
                  <option value="Noodles / Dim Sum">🍜 Noodles / Dim Sum</option>
                  <option value="Japanese / Asian">🍱 Japanese / Korean</option>
                  <option value="Fast Food / Western">🍔 Fast Food / Western</option>
                  <option value="Taiwanese / Tea">🧋 Taiwanese / Drinks</option>
                  <option value="Other">🍴 Other Cuisine</option>
                </select>
              </div>
              <div>
                <label for="restNotes" class="block text-xs font-bold text-slate-600 uppercase mb-1">Notes / Distance</label>
                <input type="text" id="restNotes" placeholder="e.g. 8m walk, good lunch sets" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-pkc-blue focus:border-transparent bg-slate-50/50">
              </div>
            </div>

            <button type="submit" class="mt-1 w-full bg-pkc-navy hover:bg-pkc-blue text-white font-black text-sm py-3 px-4 rounded-xl transition duration-150 flex items-center justify-center gap-2 shadow-md hover:shadow-lg transform active:scale-98">
              <i class="fa-solid fa-plus"></i> Add To Delivery List
            </button>
          </form>
        </div>

        <!-- Active Restaurant List Card -->
        <div class="bg-white p-5 sm:p-6 rounded-3xl shadow-xl border-2 border-slate-200/80 flex flex-col max-h-[520px]">
          <div class="flex items-center justify-between mb-3 pb-2 border-b border-slate-100">
            <div>
              <h2 class="text-base font-black text-pkc-navy flex items-center gap-2">
                <i class="fa-solid fa-utensils text-pkc-gold"></i> Options
                <span id="activeCountBadge" class="ml-1 bg-amber-100 text-pkc-navy text-xs px-2.5 py-0.5 rounded-full font-black border border-amber-300">0 Active</span>
              </h2>
            </div>
            <div class="flex gap-1">
              <button id="selectAllBtn" class="text-xs text-pkc-blue hover:text-pkc-navy font-bold px-2 py-1 rounded-lg hover:bg-blue-50 transition">Select All</button>
              <button id="deselectAllBtn" class="text-xs text-slate-400 hover:text-slate-600 font-bold px-2 py-1 rounded-lg hover:bg-slate-100 transition">Clear Selected</button>
            </div>
          </div>

          <!-- Restaurant Scrollable List -->
          <div id="restaurantList" class="overflow-y-auto space-y-2 pr-1 flex-grow min-h-[200px]">
            <!-- Dynamic Items -->
          </div>

          <!-- Footer stats & clear -->
          <div class="mt-3 pt-3 border-t border-slate-100 flex items-center justify-between text-xs text-slate-500 font-medium">
            <span id="totalCountText">Total: 0 options</span>
            <button id="clearAllBtn" class="text-red-500 hover:text-red-700 font-bold hover:underline flex items-center gap-1 transition">
              <i class="fa-solid fa-trash-can"></i> Clear List
            </button>
          </div>
        </div>

      </section>
    </div>

    <!-- PAGE 2: GROUP ORDER SHEET VIEW -->
    <div id="pageOrders" class="hidden flex-col gap-6">
      
      <!-- Top Banner: Selected Restaurant Info -->
      <div class="bg-gradient-to-r from-pkc-navy via-blue-900 to-pkc-navy text-white p-6 rounded-3xl shadow-xl border-2 border-amber-400 flex flex-col sm:flex-row items-center justify-between gap-4">
        <div class="flex items-center gap-4 text-center sm:text-left">
          <div class="w-14 h-14 bg-amber-400 text-pkc-navy rounded-2xl flex items-center justify-center text-3xl font-black shadow-lg">
            🍜
          </div>
          <div>
            <div class="flex items-center gap-2 justify-center sm:justify-start">
              <span class="bg-amber-400 text-pkc-navy text-[10px] font-black uppercase px-2.5 py-0.5 rounded-full">Ordering From</span>
            </div>
            <h2 id="orderTargetRestaurant" class="text-2xl sm:text-3xl font-black text-white mt-0.5">
              No Restaurant Selected
            </h2>
            <p class="text-xs text-amber-200 mt-0.5">List what everyone wants to order for today's staff lunch delivery!</p>
          </div>
        </div>
        
        <div class="flex gap-2">
          <button id="changeTargetRestBtn" class="bg-white/10 hover:bg-white/20 text-white font-bold text-xs px-4 py-2.5 rounded-xl border border-white/20 transition flex items-center gap-1.5">
            <i class="fa-solid fa-arrows-rotate"></i> Change Restaurant
          </button>
        </div>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start">
        
        <!-- LEFT COLUMN: Add Colleague Order Form (4 cols) -->
        <section class="lg:col-span-4 bg-white p-6 rounded-3xl shadow-xl border-2 border-slate-200/80">
          <h3 class="text-lg font-black text-pkc-navy mb-4 flex items-center gap-2">
            <i class="fa-solid fa-user-plus text-pkc-gold"></i> Add Colleague Order
          </h3>

          <form id="addOrderForm" class="flex flex-col gap-3.5">
            <div>
              <label for="orderName" class="block text-xs font-bold text-slate-700 uppercase mb-1">
                Colleague Name *
              </label>
              <input type="text" id="orderName" placeholder="e.g. Mr. Wong / Ms. Chan" required class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-pkc-blue bg-slate-50/50 font-medium">
            </div>

            <div>
              <label for="orderItem" class="block text-xs font-bold text-slate-700 uppercase mb-1">
                Menu Item / Dish *
              </label>
              <input type="text" id="orderItem" placeholder="e.g. Baked Pork Chop Rice w/ Ice Lemon Tea" required class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-pkc-blue bg-slate-50/50 font-medium">
            </div>

            <div class="grid grid-cols-2 gap-3">
              <div>
                <label for="orderPrice" class="block text-xs font-bold text-slate-700 uppercase mb-1">
                  Price HKD ($)
                </label>
                <input type="number" id="orderPrice" step="0.5" placeholder="e.g. 58" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-pkc-blue bg-slate-50/50 font-medium">
              </div>
              <div>
                <label for="orderNotes" class="block text-xs font-bold text-slate-700 uppercase mb-1">
                  Special Request
                </label>
                <input type="text" id="orderNotes" placeholder="e.g. Less ice, no spicy" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-pkc-blue bg-slate-50/50 font-medium">
              </div>
            </div>

            <button type="submit" class="mt-2 w-full bg-gradient-to-r from-pkc-navy to-pkc-blue hover:from-pkc-blue hover:to-blue-700 text-white font-black text-sm py-3 px-4 rounded-xl transition duration-150 flex items-center justify-center gap-2 shadow-md hover:shadow-lg">
              <i class="fa-solid fa-cart-plus"></i> Add To Order Sheet
            </button>
          </form>
        </section>

        <!-- RIGHT COLUMN: Order Sheet Table & Actions (8 cols) -->
        <section class="lg:col-span-8 bg-white p-6 rounded-3xl shadow-xl border-2 border-slate-200/80 flex flex-col gap-4">
          
          <!-- Order Sheet Header Controls -->
          <div class="flex flex-col sm:flex-row items-center justify-between gap-3 pb-3 border-b border-slate-100">
            <div>
              <h3 class="text-lg font-black text-pkc-navy flex items-center gap-2">
                <i class="fa-solid fa-list-check text-pkc-gold"></i> Group Order List
              </h3>
              <p class="text-xs text-slate-500">Summary of all colleague items for today's order</p>
            </div>

            <div class="flex items-center gap-2 w-full sm:w-auto">
              <button id="copySummaryBtn" class="flex-1 sm:flex-none bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-xs px-3.5 py-2 rounded-xl transition flex items-center justify-center gap-1.5 shadow-md">
                <i class="fa-solid fa-copy"></i> Copy Order Summary
              </button>
              <button id="clearOrdersBtn" class="bg-red-50 hover:bg-red-100 text-red-600 font-bold text-xs px-3 py-2 rounded-xl transition flex items-center justify-center gap-1 border border-red-200">
                <i class="fa-solid fa-trash-can"></i> Clear All
              </button>
            </div>
          </div>

          <!-- Table Display -->
          <div class="overflow-x-auto rounded-2xl border border-slate-200">
            <table class="w-full text-left border-collapse">
              <thead>
                <tr class="bg-slate-100 text-pkc-navy text-xs uppercase font-black border-b border-slate-200">
                  <th class="py-3 px-4 w-1/4">Name</th>
                  <th class="py-3 px-4 w-5/12">Menu Dish / Item</th>
                  <th class="py-3 px-4 w-1/6">Price</th>
                  <th class="py-3 px-4 w-1/6 text-right">Action</th>
                </tr>
              </thead>
              <tbody id="orderTableBody" class="divide-y divide-slate-100 text-xs sm:text-sm font-medium">
                <!-- Dynamic Rows -->
              </tbody>
            </table>
          </div>

          <!-- Total Bill & Count Footer Card -->
          <div class="bg-amber-50 rounded-2xl p-4 border border-amber-200 flex flex-col sm:flex-row items-center justify-between gap-3 mt-2">
            <div class="flex items-center gap-3">
              <div class="w-10 h-10 bg-amber-400 text-pkc-navy font-black rounded-xl flex items-center justify-center text-lg">
                💰
              </div>
              <div>
                <span class="text-xs font-bold text-amber-900 uppercase">Estimated Total</span>
                <div class="text-xl font-black text-pkc-navy" id="totalPriceDisplay">$0 HKD</div>
              </div>
            </div>

            <div class="flex items-center gap-2">
              <button id="goToSplitterBtn" class="bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-700 hover:to-teal-700 text-white font-black text-xs px-4 py-2.5 rounded-xl shadow transition flex items-center gap-1.5">
                <i class="fa-solid fa-calculator"></i> Calculate Split Payments 💳
              </button>
            </div>
          </div>

        </section>
      </div>

    </div>

    <!-- PAGE 3: PAYMENT SPLITTER VIEW -->
    <div id="pageSplitter" class="hidden flex-col gap-6">
      
      <!-- Top Banner: Payment Header -->
      <div class="bg-gradient-to-r from-emerald-800 via-teal-900 to-emerald-900 text-white p-6 rounded-3xl shadow-xl border-2 border-emerald-400 flex flex-col sm:flex-row items-center justify-between gap-4">
        <div class="flex items-center gap-4 text-center sm:text-left">
          <div class="w-14 h-14 bg-emerald-400 text-slate-900 rounded-2xl flex items-center justify-center text-3xl font-black shadow-lg">
            💳
          </div>
          <div>
            <span class="bg-emerald-400 text-slate-900 text-[10px] font-black uppercase px-2.5 py-0.5 rounded-full">Fair Settlement</span>
            <h2 class="text-2xl sm:text-3xl font-black text-white mt-0.5">
              Payment Splitter & Reimbursements
            </h2>
            <p class="text-xs text-emerald-100 mt-0.5">Pay strictly for your own dishes + choose how delivery & discounts are shared!</p>
          </div>
        </div>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start">
        
        <!-- LEFT COLUMN: Payment Configuration Controls (4 cols) -->
        <section class="lg:col-span-4 bg-white p-6 rounded-3xl shadow-xl border-2 border-slate-200/80 flex flex-col gap-5">
          <h3 class="text-lg font-black text-pkc-navy flex items-center gap-2">
            <i class="fa-solid fa-sliders text-emerald-600"></i> Settlement Settings
          </h3>

          <!-- Who Paid Upfront -->
          <div>
            <label for="payerSelect" class="block text-xs font-bold text-slate-700 uppercase mb-1">
              Who Paid Upfront? (Receiver) *
            </label>
            <select id="payerSelect" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-slate-50/50 font-bold text-pkc-navy">
              <!-- Dynamically Populated -->
            </select>
          </div>

          <!-- Payer FPS / PayMe Account Details -->
          <div>
            <label for="payerFpsInput" class="block text-xs font-bold text-slate-700 uppercase mb-1">
              Payer's FPS / PayMe Info
            </label>
            <input type="text" id="payerFpsInput" placeholder="e.g. FPS Phone: 91234567 / FPS ID" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-slate-50/50 font-medium">
            <p class="text-[10px] text-slate-400 mt-1">Included in the WhatsApp copy message for quick transfers!</p>
          </div>

          <!-- Fee Splitting Mode -->
          <div>
            <label for="splitMethodSelect" class="block text-xs font-bold text-slate-700 uppercase mb-1">
              Delivery & Discount Splitting Mode
            </label>
            <select id="splitMethodSelect" class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-slate-50/50 font-bold text-slate-800">
              <option value="equal">⚖️ Equal Share (Split fees evenly per head)</option>
              <option value="proportional">📊 Proportional Share (% of meal cost)</option>
              <option value="exact">🎯 Exact Dish Price Only (No extra adjustments)</option>
            </select>
          </div>

          <!-- Shared Delivery Fee -->
          <div>
            <label for="extraChargesInput" class="block text-xs font-bold text-slate-700 uppercase mb-1">
              Shared Delivery Fee / Tip (HKD $)
            </label>
            <div class="relative">
              <span class="absolute left-3.5 top-2.5 text-slate-400 font-bold">$</span>
              <input type="number" id="extraChargesInput" step="0.5" value="0" placeholder="0" class="w-full pl-8 pr-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-slate-50/50 font-bold">
            </div>
          </div>

          <!-- Shared Discount -->
          <div>
            <label for="discountInput" class="block text-xs font-bold text-slate-700 uppercase mb-1">
              Shared Discount / Promo (HKD $)
            </label>
            <div class="relative">
              <span class="absolute left-3.5 top-2.5 text-slate-400 font-bold">-$</span>
              <input type="number" id="discountInput" step="0.5" value="0" placeholder="0" class="w-full pl-9 pr-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-slate-50/50 font-bold text-emerald-600">
            </div>
          </div>

          <!-- Payment Math Summary Box -->
          <div class="bg-slate-50 p-4 rounded-2xl border border-slate-200 text-xs font-semibold text-slate-600 space-y-2">
            <div class="flex justify-between">
              <span>Dishes Subtotal:</span>
              <span id="splitSubtotalText" class="font-bold text-slate-800">$0.0</span>
            </div>
            <div class="flex justify-between">
              <span>Delivery / Extras:</span>
              <span id="splitExtraText" class="font-bold text-slate-800">+$0.0</span>
            </div>
            <div class="flex justify-between">
              <span>Discounts:</span>
              <span id="splitDiscountText" class="font-bold text-emerald-600">-$0.0</span>
            </div>
            <div class="pt-2 border-t border-slate-200 flex justify-between text-sm font-black text-pkc-navy">
              <span>Grand Total:</span>
              <span id="splitGrandTotalText">$0.0 HKD</span>
            </div>
          </div>
        </section>

        <!-- RIGHT COLUMN: Itemized Individual Settlement Table (8 cols) -->
        <section class="lg:col-span-8 bg-white p-6 rounded-3xl shadow-xl border-2 border-slate-200/80 flex flex-col gap-4">
          
          <div class="flex flex-col sm:flex-row items-center justify-between gap-3 pb-3 border-b border-slate-100">
            <div>
              <h3 class="text-lg font-black text-pkc-navy flex items-center gap-2">
                <i class="fa-solid fa-receipt text-emerald-600"></i> Itemized Colleague Breakdown
              </h3>
              <p class="text-xs text-slate-500">Each member pays for their specific dishes + allocated shared fee</p>
            </div>

            <button id="copyPaymentSummaryBtn" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-xs px-4 py-2.5 rounded-xl transition flex items-center justify-center gap-1.5 shadow-md">
              <i class="fa-solid fa-copy"></i> Copy Itemized WhatsApp List 💬
            </button>
          </div>

          <!-- Settlement Table -->
          <div class="overflow-x-auto rounded-2xl border border-slate-200">
            <table class="w-full text-left border-collapse">
              <thead>
                <tr class="bg-slate-100 text-pkc-navy text-xs uppercase font-black border-b border-slate-200">
                  <th class="py-3 px-4 w-1/3">Colleague & Dishes</th>
                  <th class="py-3 px-4">Dish Cost</th>
                  <th class="py-3 px-4">Shared Adj.</th>
                  <th class="py-3 px-4">Final Owed</th>
                  <th class="py-3 px-4 text-right">Status</th>
                </tr>
              </thead>
              <tbody id="settlementTableBody" class="divide-y divide-slate-100 text-xs sm:text-sm font-medium">
                <!-- Dynamic Itemized Settlement Rows -->
              </tbody>
            </table>
          </div>

        </section>

      </div>

    </div>

  </main>

  <!-- FOOTER -->
  <footer class="bg-slate-900 text-slate-400 text-xs py-5 px-6 text-center border-t border-slate-800 mt-8 relative z-10">
    <p class="font-semibold">© Food Delivery Department • Pui Kiu College Staff Lunch Picker Tool</p>
    <p class="text-[11px] text-slate-500 mt-1">Designed with 💖 for Teachers & Staff in Tai Wai & Sha Tin</p>
  </footer>

  <!-- WINNER CELEBRATION MODAL -->
  <div id="winnerModal" class="fixed inset-0 bg-slate-950/70 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden opacity-0 transition-all duration-300">
    <div class="bg-white rounded-3xl shadow-2xl max-w-md w-full p-6 text-center transform scale-90 transition-all duration-300 border-4 border-pkc-yellow relative overflow-hidden" id="modalCard">
      <div class="absolute -top-12 -left-12 w-28 h-28 bg-amber-300 rounded-full opacity-30"></div>
      <div class="absolute -bottom-12 -right-12 w-28 h-28 bg-pkc-blue rounded-full opacity-30"></div>

      <div class="w-20 h-20 bg-gradient-to-tr from-amber-400 to-amber-200 text-pkc-navy rounded-3xl flex items-center justify-center mx-auto mb-3 text-4xl shadow-lg border-2 border-amber-300 animate-bounce">
        🏆
      </div>

      <span class="inline-block bg-amber-100 text-amber-800 text-[10px] font-black uppercase tracking-widest px-3 py-1 rounded-full mb-1 border border-amber-300">
        Food Order Decided!
      </span>
      
      <h3 id="winnerTitle" class="text-2xl sm:text-3xl font-black text-pkc-navy mb-2 leading-tight">
        Restaurant Name
      </h3>
      
      <div class="inline-flex items-center gap-1 bg-blue-50 border border-blue-200 text-pkc-blue text-xs font-bold px-3 py-1 rounded-full mb-3" id="winnerCuisine">
        Cuisine Type
      </div>

      <p id="winnerNotes" class="text-sm text-slate-600 bg-slate-50 p-3.5 rounded-2xl border border-slate-100 mb-4 italic">
        "Optional notes here"
      </p>

      <div class="bg-amber-50 text-amber-900 text-xs font-bold p-2.5 rounded-xl border border-amber-200 mb-5 flex items-center justify-center gap-1.5">
        <span>🥳</span> <span id="funnyQuote">No arguments allowed—the Food Delivery Dept has spoken!</span>
      </div>

      <div class="flex flex-col gap-2">
        <button id="startOrderFromModalBtn" class="w-full bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-700 hover:to-teal-700 text-white font-black py-3 px-4 rounded-xl text-sm transition shadow-lg flex items-center justify-center gap-2">
          <i class="fa-solid fa-clipboard-list"></i> Start Group Order For This Spot! 📝
        </button>
        <div class="flex gap-2">
          <button id="spinAgainModalBtn" class="flex-1 bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold py-2.5 px-3 rounded-xl text-xs transition">
            Re-spin 🎲
          </button>
          <button id="closeModalBtn" class="flex-1 bg-pkc-navy hover:bg-pkc-blue text-white font-bold py-2.5 px-3 rounded-xl text-xs transition">
            Close ❌
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- CUSTOM CONFIRMATION & INPUT MODAL -->
  <div id="customDialogModal" class="fixed inset-0 bg-slate-950/70 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden transition-all duration-200">
    <div class="bg-white rounded-2xl shadow-2xl max-w-sm w-full p-5 border-2 border-slate-200 relative text-left">
      <h3 id="dialogTitle" class="text-base font-black text-pkc-navy mb-1">Confirmation</h3>
      <p id="dialogMessage" class="text-xs text-slate-600 mb-3">Are you sure?</p>
      <input type="text" id="dialogInput" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg mb-4 focus:ring-2 focus:ring-pkc-blue focus:outline-none hidden">
      <div class="flex justify-end gap-2">
        <button id="dialogCancelBtn" class="px-3 py-1.5 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-bold rounded-lg transition">Cancel</button>
        <button id="dialogConfirmBtn" class="px-3.5 py-1.5 bg-pkc-navy hover:bg-pkc-blue text-white text-xs font-bold rounded-lg transition">Confirm</button>
      </div>
    </div>
  </div>

  <!-- TOAST NOTIFICATION -->
  <div id="toastNotification" class="fixed bottom-5 right-5 z-50 bg-slate-900 text-white text-xs font-bold px-4 py-3 rounded-xl shadow-2xl border border-slate-700 flex items-center gap-2 transform translate-y-12 opacity-0 transition-all duration-300 pointer-events-none">
    <span id="toastIcon">ℹ️</span>
    <span id="toastText">Notification message</span>
  </div>

  <script>
    // SAFE FIREBASE INITIALIZATION
    const rawFirebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {
      apiKey: "",
      authDomain: "",
      projectId: ""
    };

    let db = null;
    let auth = null;
    let isCloudEnabled = false;
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'pkc-lunch-wheel-app';

    if (typeof firebase !== 'undefined' && rawFirebaseConfig && rawFirebaseConfig.apiKey && rawFirebaseConfig.apiKey !== "") {
      try {
        if (!firebase.apps.length) {
          firebase.initializeApp(rawFirebaseConfig);
        }
        db = firebase.firestore();
        auth = firebase.auth();
        isCloudEnabled = true;
      } catch (e) {
        console.warn("Firebase initialization skipped, using Local Storage mode.", e);
      }
    }

    // PRE-LOADED RESTAURANTS
    const PRELOADED_RESTAURANTS = [
      { id: '1', name: 'Tai Wai Dining Room', cuisine: 'Hong Kong / Cafe', notes: 'Classic HK set lunch, ~8 min walk', active: true },
      { id: '2', name: 'Tai Wai Eat Good Food (大圍好食)', cuisine: 'Noodles / Dim Sum', notes: 'Great cart noodles & snacks', active: true },
      { id: '3', name: 'Lung Wah Hotel', cuisine: 'Hong Kong / Cafe', notes: 'Famous roast pigeon, spacious seating', active: true },
      { id: '4', name: 'CoCo Ichibanya (Sha Tin)', cuisine: 'Japanese / Asian', notes: 'Japanese curry rice options', active: true },
      { id: '5', name: 'McDonald\'s Tai Wai', cuisine: 'Fast Food / Western', notes: 'Quick & reliable fast lunch', active: true },
      { id: '6', name: 'TamJai SamGor Mixian', cuisine: 'Noodles / Dim Sum', notes: 'Spicy noodle broth customization', active: true },
      { id: '7', name: 'EAT (Tai Wai Plaza)', cuisine: 'Hong Kong / Cafe', notes: 'Quality western/baked rice sets', active: true },
      { id: '8', name: 'Hanabishi Ramen', cuisine: 'Japanese / Asian', notes: 'Rich Tonkotsu ramen in Tai Wai', active: true },
      { id: '9', name: 'Cafe de Coral (Sun Chui)', cuisine: 'Hong Kong / Cafe', notes: 'Fast HK meal sets close by', active: true },
      { id: '10', name: 'Pho Thai', cuisine: 'Taiwanese / Tea', notes: 'Flavorful Thai noodles & iced tea', active: true }
    ];

    const SLICE_COLORS = [
      { bg: '#0f2b5c', text: '#ffffff' },
      { bg: '#f59e0b', text: '#0f2b5c' },
      { bg: '#1e40af', text: '#ffffff' },
      { bg: '#10b981', text: '#ffffff' },
      { bg: '#8b5cf6', text: '#ffffff' },
      { bg: '#ef4444', text: '#ffffff' },
      { bg: '#06b6d4', text: '#ffffff' },
      { bg: '#f97316', text: '#ffffff' },
      { bg: '#ec4899', text: '#ffffff' },
      { bg: '#64748b', text: '#ffffff' }
    ];

    const FUNNY_SPIN_QUOTES = [
      "Consulting the culinary oracle...",
      "Calculating maximum deliciousness...",
      "Hunger levels critical! Choosing food...",
      "Scanning Sha Tin & Tai Wai delicacies...",
      "Deploying food delivery drones...",
      "Synthesizing teacher lunch cravings..."
    ];

    const FUNNY_VICTORY_QUOTES = [
      "No arguments allowed—the wheel has dictated your lunch!",
      "Staff room order finalized. Bon Appétit!",
      "The universe has spoken. Prepare your appetite!",
      "Zero debates required today. Time to eat!",
      "Your tastebuds will thank the Food Delivery Dept!"
    ];

    // STATE VARIABLES
    let currentRoomCode = "PKC-STAFF";
    let restaurants = [];
    let groupOrders = [];
    let currentSelectedTargetRest = "Tai Wai Dining Room";
    let selectedPayer = "";
    let payerFpsInfo = "";
    let splitMethod = "equal";
    let extraCharges = 0;
    let extraDiscount = 0;
    let paidStatusMap = {};
    let unsubscribeFirestore = null;
    let isRemoteUpdate = false;

    let currentRotation = 0;
    let isSpinning = false;
    let spinAnimId = null;
    let soundEnabled = true;
    let audioCtx = null;
    let lastSectorIndex = -1;

    function showToast(message, icon = 'ℹ️') {
      const toast = document.getElementById('toastNotification');
      const toastText = document.getElementById('toastText');
      const toastIcon = document.getElementById('toastIcon');

      toastText.textContent = message;
      toastIcon.textContent = icon;

      toast.classList.remove('translate-y-12', 'opacity-0');
      toast.classList.add('translate-y-0', 'opacity-100');

      setTimeout(() => {
        toast.classList.remove('translate-y-0', 'opacity-100');
        toast.classList.add('translate-y-12', 'opacity-0');
      }, 3000);
    }

    function showCustomDialog({ title, message, showInput = false, defaultValue = '', onConfirm }) {
      const dialog = document.getElementById('customDialogModal');
      const dialogTitle = document.getElementById('dialogTitle');
      const dialogMessage = document.getElementById('dialogMessage');
      const dialogInput = document.getElementById('dialogInput');
      const confirmBtn = document.getElementById('dialogConfirmBtn');
      const cancelBtn = document.getElementById('dialogCancelBtn');

      dialogTitle.textContent = title;
      dialogMessage.textContent = message;

      if (showInput) {
        dialogInput.value = defaultValue;
        dialogInput.classList.remove('hidden');
      } else {
        dialogInput.classList.add('hidden');
      }

      dialog.classList.remove('hidden');

      const cleanup = () => {
        dialog.classList.add('hidden');
        confirmBtn.onclick = null;
        cancelBtn.onclick = null;
      };

      confirmBtn.onclick = () => {
        const value = showInput ? dialogInput.value.trim() : true;
        cleanup();
        if (onConfirm) onConfirm(value);
      };

      cancelBtn.onclick = () => {
        cleanup();
      };
    }

    // AUDIO SYNTHESIZER
    function initAudio() {
      if (!audioCtx) {
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      }
      if (audioCtx.state === 'suspended') {
        audioCtx.resume();
      }
    }

    function playTickSound() {
      if (!soundEnabled) return;
      try {
        initAudio();
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(600, audioCtx.currentTime);
        osc.frequency.exponentialRampToValueAtTime(150, audioCtx.currentTime + 0.04);
        gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.04);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.04);
      } catch (e) {}
    }

    function playVictorySound() {
      if (!soundEnabled) return;
      try {
        initAudio();
        const notes = [261.63, 329.63, 392.00, 523.25];
        notes.forEach((freq, idx) => {
          const osc = audioCtx.createOscillator();
          const gain = audioCtx.createGain();
          osc.type = 'sine';
          osc.frequency.setValueAtTime(freq, audioCtx.currentTime + idx * 0.1);
          gain.gain.setValueAtTime(0.2, audioCtx.currentTime + idx * 0.1);
          gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + idx * 0.1 + 0.3);
          osc.connect(gain);
          gain.connect(audioCtx.destination);
          osc.start(audioCtx.currentTime + idx * 0.1);
          osc.stop(audioCtx.currentTime + idx * 0.1 + 0.3);
        });
      } catch (e) {}
    }

    // INITIALIZATION FUNCTION
    async function init() {
      loadFromLocalStorage();
      if (restaurants.length === 0) {
        restaurants = [...PRELOADED_RESTAURANTS];
        saveToLocalStorage();
      }

      const syncIndicator = document.getElementById('syncIndicator');

      if (isCloudEnabled && typeof firebase !== 'undefined' && db && auth) {
        syncIndicator.className = "bg-emerald-500 text-white text-[10px] font-black uppercase px-2.5 py-0.5 rounded-full shadow border border-emerald-300 flex items-center gap-1 animate-pulse";
        syncIndicator.innerHTML = `<i class="fa-solid fa-wifi text-[9px]"></i> Live Sync ON`;
        try {
          if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
            await auth.signInWithCustomToken(__initial_auth_token);
          } else {
            await auth.signInAnonymously();
          }
          setupFirestoreListener();
        } catch (e) {
          console.warn("Auth failed, continuing in Local Storage mode", e);
        }
      } else {
        syncIndicator.className = "bg-amber-500 text-white text-[10px] font-black uppercase px-2.5 py-0.5 rounded-full shadow border border-amber-300 flex items-center gap-1";
        syncIndicator.innerHTML = `<i class="fa-solid fa-hard-drive text-[9px]"></i> Local Storage`;
      }

      renderRestaurantList();
      renderGroupOrders();
      renderSplitterPage();
      drawWheel();
      setupEventListeners();
    }

    // FIRESTORE SYNC
    function setupFirestoreListener() {
      if (!isCloudEnabled || !db || typeof firebase === 'undefined') return;
      if (unsubscribeFirestore) unsubscribeFirestore();

      const docRef = db.collection('artifacts').doc(appId)
        .collection('public').doc('data')
        .collection('rooms').doc(currentRoomCode);

      unsubscribeFirestore = docRef.onSnapshot((doc) => {
        if (doc.exists) {
          const data = doc.data();
          isRemoteUpdate = true;
          
          if (data.restaurants) restaurants = data.restaurants;
          if (data.groupOrders) groupOrders = data.groupOrders;
          if (data.targetRestaurant) currentSelectedTargetRest = data.targetRestaurant;
          if (data.selectedPayer) selectedPayer = data.selectedPayer;
          if (data.payerFpsInfo !== undefined) {
            payerFpsInfo = data.payerFpsInfo;
            document.getElementById('payerFpsInput').value = payerFpsInfo;
          }
          if (data.splitMethod) {
            splitMethod = data.splitMethod;
            document.getElementById('splitMethodSelect').value = splitMethod;
          }
          if (data.extraCharges !== undefined) {
            extraCharges = data.extraCharges;
            document.getElementById('extraChargesInput').value = extraCharges;
          }
          if (data.extraDiscount !== undefined) {
            extraDiscount = data.extraDiscount;
            document.getElementById('discountInput').value = extraDiscount;
          }
          if (data.paidStatusMap) paidStatusMap = data.paidStatusMap;

          renderRestaurantList();
          renderGroupOrders();
          renderSplitterPage();
          drawWheel();
          
          isRemoteUpdate = false;
        } else {
          pushToCloud();
        }
      }, (err) => {
        console.error("Cloud Sync Error:", err);
      });
    }

    function pushToCloud() {
      saveToLocalStorage();
      if (!isCloudEnabled || !db || isRemoteUpdate || typeof firebase === 'undefined') return;

      const docRef = db.collection('artifacts').doc(appId)
        .collection('public').doc('data')
        .collection('rooms').doc(currentRoomCode);

      docRef.set({
        restaurants,
        groupOrders,
        targetRestaurant: currentSelectedTargetRest,
        selectedPayer,
        payerFpsInfo,
        splitMethod,
        extraCharges,
        extraDiscount,
        paidStatusMap,
        updatedAt: firebase.firestore.FieldValue.serverTimestamp()
      }, { merge: true }).catch(err => {
        console.error("Cloud Sync Push Failed:", err);
      });
    }

    function saveToLocalStorage() {
      localStorage.setItem('pkc_food_room_code', currentRoomCode);
      localStorage.setItem('pkc_food_delivery_dept', JSON.stringify(restaurants));
      localStorage.setItem('pkc_food_group_orders', JSON.stringify(groupOrders));
      localStorage.setItem('pkc_food_target_rest', currentSelectedTargetRest);
      localStorage.setItem('pkc_food_selected_payer', selectedPayer);
      localStorage.setItem('pkc_food_payer_fps', payerFpsInfo);
      localStorage.setItem('pkc_food_split_method', splitMethod);
      localStorage.setItem('pkc_food_extra_charges', extraCharges.toString());
      localStorage.setItem('pkc_food_extra_discount', extraDiscount.toString());
      localStorage.setItem('pkc_food_paid_status', JSON.stringify(paidStatusMap));
    }

    function loadFromLocalStorage() {
      const storedRoom = localStorage.getItem('pkc_food_room_code');
      if (storedRoom) {
        currentRoomCode = storedRoom.toUpperCase().trim();
        document.getElementById('roomCodeInput').value = currentRoomCode;
      }

      const storedR = localStorage.getItem('pkc_food_delivery_dept');
      if (storedR) {
        try { restaurants = JSON.parse(storedR); } catch (e) { restaurants = []; }
      }
      const storedO = localStorage.getItem('pkc_food_group_orders');
      if (storedO) {
        try { groupOrders = JSON.parse(storedO); } catch (e) { groupOrders = []; }
      }
      const storedT = localStorage.getItem('pkc_food_target_rest');
      if (storedT) currentSelectedTargetRest = storedT;

      selectedPayer = localStorage.getItem('pkc_food_selected_payer') || "";
      payerFpsInfo = localStorage.getItem('pkc_food_payer_fps') || "";
      splitMethod = localStorage.getItem('pkc_food_split_method') || "equal";
      extraCharges = parseFloat(localStorage.getItem('pkc_food_extra_charges') || '0');
      extraDiscount = parseFloat(localStorage.getItem('pkc_food_extra_discount') || '0');
      
      const storedPaid = localStorage.getItem('pkc_food_paid_status');
      if (storedPaid) {
        try { paidStatusMap = JSON.parse(storedPaid); } catch (e) { paidStatusMap = {}; }
      }

      document.getElementById('payerFpsInput').value = payerFpsInfo;
      document.getElementById('splitMethodSelect').value = splitMethod;
      document.getElementById('extraChargesInput').value = extraCharges;
      document.getElementById('discountInput').value = extraDiscount;
    }

    function switchTab(target) {
      const pageSpinner = document.getElementById('pageSpinner');
      const pageOrders = document.getElementById('pageOrders');
      const pageSplitter = document.getElementById('pageSplitter');

      const navTabSpinner = document.getElementById('navTabSpinner');
      const navTabOrders = document.getElementById('navTabOrders');
      const navTabSplitter = document.getElementById('navTabSplitter');

      pageSpinner.classList.add('hidden');
      pageOrders.classList.add('hidden');
      pageOrders.classList.remove('flex');
      pageSplitter.classList.add('hidden');
      pageSplitter.classList.remove('flex');

      navTabSpinner.className = "nav-tab bg-blue-900/60 hover:bg-blue-900 text-white font-bold text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl border-t border-x border-blue-700/60 flex items-center gap-2 transition whitespace-nowrap";
      navTabOrders.className = "nav-tab bg-blue-900/60 hover:bg-blue-900 text-white font-bold text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl border-t border-x border-blue-700/60 flex items-center gap-2 transition whitespace-nowrap";
      navTabSplitter.className = "nav-tab bg-blue-900/60 hover:bg-blue-900 text-white font-bold text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl border-t border-x border-blue-700/60 flex items-center gap-2 transition whitespace-nowrap";

      if (target === 'spinner') {
        pageSpinner.classList.remove('hidden');
        navTabSpinner.className = "nav-tab active-tab bg-white text-pkc-navy font-black text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl shadow-md border-t-2 border-x-2 border-white flex items-center gap-2 transition whitespace-nowrap";
        drawWheel();
      } else if (target === 'orders') {
        pageOrders.classList.remove('hidden');
        pageOrders.classList.add('flex');
        navTabOrders.className = "nav-tab active-tab bg-white text-pkc-navy font-black text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl shadow-md border-t-2 border-x-2 border-white flex items-center gap-2 transition whitespace-nowrap";
        renderGroupOrders();
      } else if (target === 'splitter') {
        pageSplitter.classList.remove('hidden');
        pageSplitter.classList.add('flex');
        navTabSplitter.className = "nav-tab active-tab bg-white text-pkc-navy font-black text-xs sm:text-sm px-5 py-2.5 rounded-t-2xl shadow-md border-t-2 border-x-2 border-white flex items-center gap-2 transition whitespace-nowrap";
        renderSplitterPage();
      }
    }

    function drawWheel() {
      const canvas = document.getElementById('wheelCanvas');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');
      const activeSpots = restaurants.filter(r => r.active);
      const width = canvas.width;
      const height = canvas.height;
      const centerX = width / 2;
      const centerY = height / 2;
      const radius = width / 2 - 16;

      ctx.clearRect(0, 0, width, height);

      if (activeSpots.length === 0) {
        ctx.save();
        ctx.beginPath();
        ctx.arc(centerX, centerY, radius, 0, 2 * Math.PI);
        ctx.fillStyle = '#f8fafc';
        ctx.fill();
        ctx.lineWidth = 4;
        ctx.strokeStyle = '#cbd5e1';
        ctx.stroke();

        ctx.fillStyle = '#64748b';
        ctx.font = 'bold 16px sans-serif';
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillText('Please enable at least 1 restaurant!', centerX, centerY);
        ctx.restore();
        return;
      }

      const numSlices = activeSpots.length;
      const sliceAngle = (2 * Math.PI) / numSlices;

      ctx.save();
      ctx.translate(centerX, centerY);
      ctx.rotate(currentRotation);

      for (let i = 0; i < numSlices; i++) {
        const spot = activeSpots[i];
        const startAngle = i * sliceAngle;
        const endAngle = startAngle + sliceAngle;
        const colorScheme = SLICE_COLORS[i % SLICE_COLORS.length];

        ctx.beginPath();
        ctx.moveTo(0, 0);
        ctx.arc(0, 0, radius, startAngle, endAngle);
        ctx.closePath();

        ctx.fillStyle = colorScheme.bg;
        ctx.fill();
        ctx.lineWidth = 3;
        ctx.strokeStyle = '#ffffff';
        ctx.stroke();

        ctx.save();
        ctx.rotate(startAngle + sliceAngle / 2);
        ctx.textAlign = 'right';
        ctx.textBaseline = 'middle';
        ctx.fillStyle = colorScheme.text;

        const fontSize = numSlices > 12 ? 11 : numSlices > 8 ? 13 : 15;
        ctx.font = `bold ${fontSize}px sans-serif`;

        let nameToDraw = spot.name;
        const maxChars = numSlices > 10 ? 13 : 18;
        if (nameToDraw.length > maxChars) {
          nameToDraw = nameToDraw.substring(0, maxChars - 2) + '..';
        }

        ctx.fillText(nameToDraw, radius - 24, 0);
        ctx.restore();
      }

      ctx.beginPath();
      ctx.arc(0, 0, 38, 0, 2 * Math.PI);
      ctx.fillStyle = '#0f2b5c';
      ctx.fill();
      ctx.lineWidth = 5;
      ctx.strokeStyle = '#f59e0b';
      ctx.stroke();

      ctx.beginPath();
      ctx.arc(0, 0, 14, 0, 2 * Math.PI);
      ctx.fillStyle = '#ffffff';
      ctx.fill();

      ctx.restore();
    }

    function spinWheel() {
      const activeSpots = restaurants.filter(r => r.active);
      const spinBtn = document.getElementById('spinBtn');
      const lightsRing = document.getElementById('lightsRing');
      const statusText = document.getElementById('statusText');
      const wheelPointer = document.getElementById('wheelPointer');

      if (activeSpots.length === 0) {
        showToast('Please enable at least one restaurant to spin!', '⚠️');
        return;
      }
      if (isSpinning) return;

      initAudio();
      isSpinning = true;
      spinBtn.disabled = true;
      spinBtn.classList.add('opacity-50', 'cursor-not-allowed');

      statusText.textContent = FUNNY_SPIN_QUOTES[Math.floor(Math.random() * FUNNY_SPIN_QUOTES.length)];
      lightsRing.classList.add('animate-pulseGlow', 'border-amber-500');

      const numSlices = activeSpots.length;
      const sliceAngle = (2 * Math.PI) / numSlices;
      const winningIndex = Math.floor(Math.random() * numSlices);

      const sliceCenterOffset = winningIndex * sliceAngle + sliceAngle / 2;
      const pointerAngle = (3 * Math.PI) / 2;
      const fullRotations = Math.floor(Math.random() * 3) + 5;

      const currentMod = currentRotation % (2 * Math.PI);
      let targetRotation = currentRotation + (fullRotations * 2 * Math.PI) + (pointerAngle - sliceCenterOffset - currentMod);

      while (targetRotation <= currentRotation + (4 * Math.PI)) {
        targetRotation += 2 * Math.PI;
      }

      const startRotation = currentRotation;
      const totalRotationChange = targetRotation - startRotation;
      const startTime = performance.now();
      const spinDuration = 4500;

      function easeOutCubic(t) {
        return 1 - Math.pow(1 - t, 3);
      }

      function animateSpin(now) {
        const elapsed = now - startTime;
        const progress = Math.min(elapsed / spinDuration, 1);
        const easedProgress = easeOutCubic(progress);

        currentRotation = startRotation + totalRotationChange * easedProgress;
        drawWheel();

        const normalizedRotation = (currentRotation % (2 * Math.PI) + 2 * Math.PI) % (2 * Math.PI);
        const pointerRelativeAngle = (pointerAngle - normalizedRotation + 2 * Math.PI) % (2 * Math.PI);
        const currentSector = Math.floor(pointerRelativeAngle / sliceAngle);

        if (currentSector !== lastSectorIndex) {
          lastSectorIndex = currentSector;
          playTickSound();

          wheelPointer.classList.remove('pointer-wiggle');
          void wheelPointer.offsetWidth;
          wheelPointer.classList.add('pointer-wiggle');
        }

        if (progress < 1) {
          spinAnimId = requestAnimationFrame(animateSpin);
        } else {
          isSpinning = false;
          spinBtn.disabled = false;
          spinBtn.classList.remove('opacity-50', 'cursor-not-allowed');
          lightsRing.classList.remove('animate-pulseGlow', 'border-amber-500');
          statusText.textContent = "Decision finalized! Bon Appétit!";

          const winner = activeSpots[winningIndex];
          currentSelectedTargetRest = winner.name;
          pushToCloud();
          playVictorySound();
          showWinnerModal(winner);
        }
      }

      spinAnimId = requestAnimationFrame(animateSpin);
    }

    function showWinnerModal(winner) {
      const winnerModal = document.getElementById('winnerModal');
      const modalCard = document.getElementById('modalCard');
      const winnerTitle = document.getElementById('winnerTitle');
      const winnerCuisine = document.getElementById('winnerCuisine');
      const winnerNotes = document.getElementById('winnerNotes');
      const funnyQuote = document.getElementById('funnyQuote');

      winnerTitle.textContent = winner.name;
      winnerCuisine.textContent = winner.cuisine || 'General Food';
      winnerNotes.textContent = winner.notes ? `"${winner.notes}"` : 'No notes registered.';
      funnyQuote.textContent = FUNNY_VICTORY_QUOTES[Math.floor(Math.random() * FUNNY_VICTORY_QUOTES.length)];

      winnerModal.classList.remove('hidden');
      setTimeout(() => {
        winnerModal.classList.remove('opacity-0');
        modalCard.classList.remove('scale-90');
        modalCard.classList.add('scale-100');
      }, 10);

      if (typeof confetti === 'function') {
        confetti({
          particleCount: 120,
          spread: 80,
          origin: { y: 0.6 },
          colors: ['#0f2b5c', '#f59e0b', '#1e40af', '#10b981', '#ef4444']
        });
      }
    }

    function closeModal() {
      const winnerModal = document.getElementById('winnerModal');
      const modalCard = document.getElementById('modalCard');

      modalCard.classList.remove('scale-100');
      modalCard.classList.add('scale-90');
      winnerModal.classList.add('opacity-0');
      setTimeout(() => {
        winnerModal.classList.add('hidden');
      }, 300);
    }

    function renderRestaurantList() {
      const restaurantListEl = document.getElementById('restaurantList');
      if (!restaurantListEl) return;
      restaurantListEl.innerHTML = '';

      if (restaurants.length === 0) {
        restaurantListEl.innerHTML = `
          <div class="text-center py-8 text-slate-400">
            <div class="text-4xl mb-2">🍽️</div>
            <p class="text-xs font-bold">No spots in list.</p>
            <p class="text-[11px] mt-1">Click <strong>"Pre-load Spots"</strong> to stock up!</p>
          </div>
        `;
      } else {
        restaurants.forEach((spot) => {
          const itemEl = document.createElement('div');
          itemEl.className = `flex items-center justify-between p-2.5 rounded-2xl border transition-all ${
            spot.active ? 'bg-white border-slate-200 shadow-sm' : 'bg-slate-50 border-slate-200 opacity-55'
          }`;

          itemEl.innerHTML = `
            <div class="flex items-center gap-2.5 min-w-0">
              <input type="checkbox" data-id="${spot.id}" ${spot.active ? 'checked' : ''} 
                class="toggle-active w-4 h-4 text-pkc-blue rounded border-slate-300 focus:ring-pkc-blue cursor-pointer">
              <div class="min-w-0">
                <p class="text-xs sm:text-sm font-black text-slate-800 truncate">${escapeHtml(spot.name)}</p>
                <div class="flex items-center gap-1.5 text-[10px] text-slate-500 font-medium">
                  <span class="bg-slate-100 text-slate-700 px-1.5 py-0.5 rounded-md border border-slate-200 font-bold">${escapeHtml(spot.cuisine)}</span>
                  ${spot.notes ? `<span class="truncate max-w-[140px]"><i class="fa-solid fa-location-dot text-amber-500"></i> ${escapeHtml(spot.notes)}</span>` : ''}
                </div>
              </div>
            </div>
            <button data-id="${spot.id}" class="delete-btn text-slate-300 hover:text-red-500 p-1.5 transition rounded-xl hover:bg-red-50 ml-2">
              <i class="fa-solid fa-trash-can text-xs"></i>
            </button>
          `;
          restaurantListEl.appendChild(itemEl);
        });
      }

      const activeCount = restaurants.filter(r => r.active).length;
      document.getElementById('activeCountBadge').textContent = `${activeCount} Active`;
      document.getElementById('totalCountText').textContent = `Total: ${restaurants.length} options`;

      drawWheel();
    }

    function renderGroupOrders() {
      const orderTargetRestaurant = document.getElementById('orderTargetRestaurant');
      const orderTableBody = document.getElementById('orderTableBody');
      const orderBadgeCount = document.getElementById('orderBadgeCount');
      const totalPriceDisplay = document.getElementById('totalPriceDisplay');

      if (!orderTableBody) return;

      orderTargetRestaurant.textContent = currentSelectedTargetRest || "General Group Order";
      orderTableBody.innerHTML = '';

      let totalPrice = 0;

      if (groupOrders.length === 0) {
        orderTableBody.innerHTML = `
          <tr>
            <td colspan="4" class="py-8 text-center text-slate-400">
              <div class="text-3xl mb-1">📝</div>
              <p class="text-xs font-bold">No orders listed yet.</p>
              <p class="text-[11px] text-slate-400 mt-0.5">Use the form on the left to add colleague orders!</p>
            </td>
          </tr>
        `;
        orderBadgeCount.classList.add('hidden');
      } else {
        groupOrders.forEach((ord, index) => {
          const priceNum = parseFloat(ord.price) || 0;
          totalPrice += priceNum;

          const tr = document.createElement('tr');
          tr.className = "hover:bg-slate-50 transition border-b border-slate-100";
          tr.innerHTML = `
            <td class="py-3 px-4 font-black text-pkc-navy">
              ${escapeHtml(ord.name)}
            </td>
            <td class="py-3 px-4">
              <div class="font-bold text-slate-800">${escapeHtml(ord.item)}</div>
              ${ord.notes ? `<div class="text-[11px] text-amber-700 bg-amber-50/80 px-2 py-0.5 rounded border border-amber-200 inline-block mt-0.5"><i class="fa-solid fa-pen"></i> ${escapeHtml(ord.notes)}</div>` : ''}
            </td>
            <td class="py-3 px-4 font-bold text-slate-700">
              ${priceNum > 0 ? `$${priceNum.toFixed(1)}` : '-'}
            </td>
            <td class="py-3 px-4 text-right">
              <button data-index="${index}" class="delete-order-btn text-slate-300 hover:text-red-500 p-1.5 transition rounded-lg hover:bg-red-50">
                <i class="fa-solid fa-trash-can"></i>
              </button>
            </td>
          `;
          orderTableBody.appendChild(tr);
        });

        orderBadgeCount.textContent = groupOrders.length;
        orderBadgeCount.classList.remove('hidden');
      }

      totalPriceDisplay.textContent = `$${totalPrice.toFixed(1)} HKD`;
    }

    function renderSplitterPage() {
      const payerSelect = document.getElementById('payerSelect');
      const settlementTableBody = document.getElementById('settlementTableBody');

      if (!payerSelect || !settlementTableBody) return;

      const colleagueMap = {};
      groupOrders.forEach(ord => {
        const nameKey = ord.name.trim();
        if (!colleagueMap[nameKey]) {
          colleagueMap[nameKey] = { name: nameKey, items: [], dishTotal: 0 };
        }
        const p = parseFloat(ord.price) || 0;
        colleagueMap[nameKey].items.push({ id: ord.id, item: ord.item, price: p, notes: ord.notes });
        colleagueMap[nameKey].dishTotal += p;
      });

      const colleagues = Object.values(colleagueMap);

      payerSelect.innerHTML = '';
      if (colleagues.length === 0) {
        payerSelect.innerHTML = `<option value="">-- No orders found --</option>`;
      } else {
        colleagues.forEach(col => {
          const opt = document.createElement('option');
          opt.value = col.name;
          opt.textContent = col.name;
          if (selectedPayer === col.name) opt.selected = true;
          payerSelect.appendChild(opt);
        });
        if (!selectedPayer || !colleagueMap[selectedPayer]) {
          selectedPayer = colleagues[0].name;
        }
      }

      let subtotal = 0;
      colleagues.forEach(c => subtotal += c.dishTotal);

      const numPeople = colleagues.length;
      const netShared = extraCharges - extraDiscount;
      const grandTotal = subtotal + netShared;

      document.getElementById('splitSubtotalText').textContent = `$${subtotal.toFixed(1)}`;
      document.getElementById('splitExtraText').textContent = `+$${extraCharges.toFixed(1)}`;
      document.getElementById('splitDiscountText').textContent = `-$${extraDiscount.toFixed(1)}`;
      document.getElementById('splitGrandTotalText').textContent = `$${grandTotal.toFixed(1)} HKD`;

      settlementTableBody.innerHTML = '';

      if (colleagues.length === 0) {
        settlementTableBody.innerHTML = `
          <tr>
            <td colspan="5" class="py-8 text-center text-slate-400">
              <div class="text-3xl mb-1">💳</div>
              <p class="text-xs font-bold">No active group orders to calculate.</p>
              <p class="text-[11px] text-slate-400 mt-0.5">Add items in the "Group Order Sheet" tab first!</p>
            </td>
          </tr>
        `;
      } else {
        colleagues.forEach(col => {
          let sharedShare = 0;
          if (splitMethod === 'equal') {
            sharedShare = numPeople > 0 ? netShared / numPeople : 0;
          } else if (splitMethod === 'proportional') {
            sharedShare = subtotal > 0 ? (col.dishTotal / subtotal) * netShared : 0;
          } else if (splitMethod === 'exact') {
            sharedShare = 0;
          }

          const finalOwed = Math.max(0, col.dishTotal + sharedShare);
          const isPayer = (col.name === selectedPayer);
          const isPaid = !!paidStatusMap[col.name];

          let dishItemsHtml = col.items.map(it => `
            <div class="flex items-center justify-between text-[11px] text-slate-600 my-0.5 bg-slate-50 p-1.5 rounded-lg border border-slate-100">
              <span class="font-medium truncate max-w-[180px]">• ${escapeHtml(it.item)}</span>
              <div class="flex items-center gap-1">
                <span class="font-bold">$</span>
                <input type="number" step="0.5" value="${it.price || ''}" placeholder="0" data-order-id="${it.id}"
                  class="price-quick-edit w-14 px-1 py-0.5 text-right text-xs font-bold border border-slate-300 rounded bg-white focus:outline-none focus:ring-1 focus:ring-emerald-500">
              </div>
            </div>
          `).join('');

          const tr = document.createElement('tr');
          tr.className = `transition border-b border-slate-100 align-top ${isPayer ? 'bg-amber-50/50' : 'hover:bg-slate-50'}`;
          
          tr.innerHTML = `
            <td class="py-3 px-4">
              <div class="font-black text-pkc-navy flex items-center gap-1.5 mb-1 text-sm">
                ${escapeHtml(col.name)}
                ${isPayer ? `<span class="bg-amber-400 text-pkc-navy text-[9px] font-black uppercase px-2 py-0.5 rounded-full border border-amber-300">Payer 👑</span>` : ''}
              </div>
              <div class="space-y-0.5">
                ${dishItemsHtml}
              </div>
            </td>
            <td class="py-3 px-4 font-black text-slate-800">
              $${col.dishTotal.toFixed(1)}
            </td>
            <td class="py-3 px-4 ${sharedShare >= 0 ? 'text-slate-500' : 'text-emerald-600'} font-bold">
              ${sharedShare >= 0 ? '+' : ''}$${sharedShare.toFixed(1)}
            </td>
            <td class="py-3 px-4 text-base font-black text-pkc-navy">
              $${finalOwed.toFixed(1)}
            </td>
            <td class="py-3 px-4 text-right">
              ${isPayer 
                ? `<span class="text-emerald-700 bg-emerald-100 px-2.5 py-1 rounded-full text-xs font-black inline-block shadow-sm">Receives $${(grandTotal - finalOwed).toFixed(1)}</span>`
                : `<div class="flex flex-col items-end gap-1.5">
                    <span class="text-slate-700 text-xs font-bold">Owes <strong class="text-pkc-navy font-black">${escapeHtml(selectedPayer)}</strong></span>
                    <button data-colleague="${escapeHtml(col.name)}" class="toggle-paid-btn text-xs font-black px-2.5 py-1 rounded-xl transition shadow-sm ${
                      isPaid 
                        ? 'bg-emerald-600 text-white hover:bg-emerald-700' 
                        : 'bg-amber-100 text-amber-900 hover:bg-amber-200 border border-amber-300'
                    }">
                      ${isPaid ? 'Paid ✅' : 'Mark as Paid ⏳'}
                    </button>
                  </div>`
              }
            </td>
          `;
          settlementTableBody.appendChild(tr);
        });
      }
    }

    function escapeHtml(str) {
      if (!str) return '';
      return str.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;").replace(/'/g, "&#039;");
    }

    function setupEventListeners() {
      const spinBtn = document.getElementById('spinBtn');
      const canvas = document.getElementById('wheelCanvas');
      const changeRoomBtn = document.getElementById('changeRoomBtn');
      const roomCodeInput = document.getElementById('roomCodeInput');
      const addForm = document.getElementById('addRestaurantForm');
      const addOrderForm = document.getElementById('addOrderForm');
      const orderTableBody = document.getElementById('orderTableBody');
      const clearOrdersBtn = document.getElementById('clearOrdersBtn');
      const copySummaryBtn = document.getElementById('copySummaryBtn');
      const payerSelect = document.getElementById('payerSelect');
      const payerFpsInput = document.getElementById('payerFpsInput');
      const splitMethodSelect = document.getElementById('splitMethodSelect');
      const extraChargesInput = document.getElementById('extraChargesInput');
      const discountInput = document.getElementById('discountInput');
      const settlementTableBody = document.getElementById('settlementTableBody');
      const copyPaymentSummaryBtn = document.getElementById('copyPaymentSummaryBtn');
      const changeTargetRestBtn = document.getElementById('changeTargetRestBtn');
      const restaurantListEl = document.getElementById('restaurantList');

      spinBtn.addEventListener('click', spinWheel);
      canvas.addEventListener('click', spinWheel);

      changeRoomBtn.addEventListener('click', () => {
        const input = roomCodeInput.value.trim().toUpperCase();
        if (input && input !== currentRoomCode) {
          currentRoomCode = input;
          saveToLocalStorage();
          if (isCloudEnabled) setupFirestoreListener();
          showToast(`Switched Room Code: ${currentRoomCode}`, '🔄');
        }
      });

      roomCodeInput.addEventListener('keydown', (e) => {
        if (e.key === 'Enter') changeRoomBtn.click();
      });

      document.getElementById('navTabSpinner').addEventListener('click', () => switchTab('spinner'));
      document.getElementById('navTabOrders').addEventListener('click', () => switchTab('orders'));
      document.getElementById('navTabSplitter').addEventListener('click', () => switchTab('splitter'));
      document.getElementById('goToSplitterBtn').addEventListener('click', () => switchTab('splitter'));

      document.getElementById('soundToggleBtn').addEventListener('click', () => {
        soundEnabled = !soundEnabled;
        const soundIcon = document.getElementById('soundIcon');
        const soundText = document.getElementById('soundText');
        if (soundEnabled) {
          soundIcon.className = "fa-solid fa-volume-high";
          soundText.textContent = "Sound ON";
        } else {
          soundIcon.className = "fa-solid fa-volume-xmark";
          soundText.textContent = "Muted";
        }
      });

      addForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const nameInput = document.getElementById('restName');
        const cuisineInput = document.getElementById('restCuisine');
        const notesInput = document.getElementById('restNotes');

        const newSpot = {
          id: Date.now().toString(),
          name: nameInput.value.trim(),
          cuisine: cuisineInput.value,
          notes: notesInput.value.trim(),
          active: true
        };

        restaurants.unshift(newSpot);
        pushToCloud();
        renderRestaurantList();

        nameInput.value = '';
        notesInput.value = '';
        showToast('Added spot to delivery list!', '🍕');
      });

      addOrderForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const orderName = document.getElementById('orderName');
        const orderItem = document.getElementById('orderItem');
        const orderPrice = document.getElementById('orderPrice');
        const orderNotes = document.getElementById('orderNotes');

        const newOrder = {
          id: Date.now().toString(),
          name: orderName.value.trim(),
          item: orderItem.value.trim(),
          price: orderPrice.value ? parseFloat(orderPrice.value) : 0,
          notes: orderNotes.value.trim()
        };

        groupOrders.push(newOrder);
        pushToCloud();
        renderGroupOrders();

        orderName.value = '';
        orderItem.value = '';
        orderPrice.value = '';
        orderNotes.value = '';
        orderName.focus();
        showToast('Order added to sheet!', '📝');
      });

      orderTableBody.addEventListener('click', (e) => {
        const deleteBtn = e.target.closest('.delete-order-btn');
        if (deleteBtn) {
          const index = parseInt(deleteBtn.getAttribute('data-index'));
          groupOrders.splice(index, 1);
          pushToCloud();
          renderGroupOrders();
        }
      });

      clearOrdersBtn.addEventListener('click', () => {
        if (groupOrders.length === 0) return;
        showCustomDialog({
          title: "Clear Order Sheet",
          message: "Are you sure you want to clear the entire order sheet for all colleagues?",
          onConfirm: () => {
            groupOrders = [];
            paidStatusMap = {};
            pushToCloud();
            renderGroupOrders();
            showToast('Order sheet cleared!', '🗑️');
          }
        });
      });

      copySummaryBtn.addEventListener('click', () => {
        if (groupOrders.length === 0) {
          showToast('Order list is empty! Add orders first.', '⚠️');
          return;
        }

        let summaryText = `🍕 *FOOD DELIVERY DEPT - GROUP ORDER*\n`;
        summaryText += `📍 *Restaurant:* ${currentSelectedTargetRest}\n`;
        summaryText += `-----------------------------------\n`;

        let total = 0;
        groupOrders.forEach((ord, idx) => {
          summaryText += `${idx + 1}. *${ord.name}*: ${ord.item}`;
          if (ord.price) {
            summaryText += ` ($${ord.price})`;
            total += parseFloat(ord.price);
          }
          if (ord.notes) {
            summaryText += ` _[Req: ${ord.notes}]_`;
          }
          summaryText += `\n`;
        });

        summaryText += `-----------------------------------\n`;
        summaryText += `📦 *Total Items:* ${groupOrders.length}\n`;
        if (total > 0) {
          summaryText += `💰 *Est. Total:* $${total.toFixed(1)} HKD\n`;
        }

        navigator.clipboard.writeText(summaryText).then(() => {
          showToast('Order summary copied to clipboard!', '📋');
        }).catch(() => {
          showToast('Failed to copy automatically.', '❌');
        });
      });

      payerSelect.addEventListener('change', (e) => {
        selectedPayer = e.target.value;
        pushToCloud();
        renderSplitterPage();
      });

      payerFpsInput.addEventListener('input', (e) => {
        payerFpsInfo = e.target.value.trim();
        pushToCloud();
      });

      splitMethodSelect.addEventListener('change', (e) => {
        splitMethod = e.target.value;
        pushToCloud();
        renderSplitterPage();
      });

      extraChargesInput.addEventListener('input', (e) => {
        extraCharges = parseFloat(e.target.value) || 0;
        pushToCloud();
        renderSplitterPage();
      });

      discountInput.addEventListener('input', (e) => {
        extraDiscount = parseFloat(e.target.value) || 0;
        pushToCloud();
        renderSplitterPage();
      });

      settlementTableBody.addEventListener('change', (e) => {
        if (e.target.classList.contains('price-quick-edit')) {
          const orderId = e.target.getAttribute('data-order-id');
          const newPrice = parseFloat(e.target.value) || 0;
          const targetOrd = groupOrders.find(o => o.id === orderId);
          if (targetOrd) {
            targetOrd.price = newPrice;
            pushToCloud();
            renderGroupOrders();
            renderSplitterPage();
          }
        }
      });

      settlementTableBody.addEventListener('click', (e) => {
        const toggleBtn = e.target.closest('.toggle-paid-btn');
        if (toggleBtn) {
          const name = toggleBtn.getAttribute('data-colleague');
          paidStatusMap[name] = !paidStatusMap[name];
          pushToCloud();
          renderSplitterPage();
        }
      });

      copyPaymentSummaryBtn.addEventListener('click', () => {
        const colleagueMap = {};
        groupOrders.forEach(ord => {
          const nameKey = ord.name.trim();
          if (!colleagueMap[nameKey]) {
            colleagueMap[nameKey] = { name: nameKey, items: [], dishTotal: 0 };
          }
          const p = parseFloat(ord.price) || 0;
          colleagueMap[nameKey].items.push({ item: ord.item, price: p });
          colleagueMap[nameKey].dishTotal += p;
        });

        const colleagues = Object.values(colleagueMap);
        if (colleagues.length === 0) {
          showToast('No orders to calculate settlement for!', '⚠️');
          return;
        }

        let subtotal = 0;
        colleagues.forEach(c => subtotal += c.dishTotal);
        const numPeople = colleagues.length;
        const netShared = extraCharges - extraDiscount;
        const grandTotal = subtotal + netShared;

        let payMsg = `💳 *LUNCH PAYMENT SETTLEMENT*\n`;
        payMsg += `📍 *Restaurant:* ${currentSelectedTargetRest}\n`;
        payMsg += `👑 *Paid Upfront By:* ${selectedPayer}\n`;
        if (payerFpsInfo) {
          payMsg += `📲 *PayMe / FPS:* ${payerFpsInfo}\n`;
        }
        payMsg += `💵 *Grand Total:* $${grandTotal.toFixed(1)} HKD\n`;
        payMsg += `-----------------------------------\n`;

        colleagues.forEach((col, idx) => {
          let sharedShare = 0;
          if (splitMethod === 'equal') {
            sharedShare = numPeople > 0 ? netShared / numPeople : 0;
          } else if (splitMethod === 'proportional') {
            sharedShare = subtotal > 0 ? (col.dishTotal / subtotal) * netShared : 0;
          }

          const finalOwed = Math.max(0, col.dishTotal + sharedShare);
          const isPaid = !!paidStatusMap[col.name];
          
          payMsg += `${idx + 1}. *${col.name}* ${isPaid ? '✅ (Paid)' : ''}\n`;
          col.items.forEach(it => {
            payMsg += `   • ${it.item} ($${it.price.toFixed(1)})\n`;
          });

          if (col.name === selectedPayer) {
            payMsg += `   👑 *(Payer - Receives remaining total)*\n\n`;
          } else {
            payMsg += `   👉 *TOTAL OWED: $${finalOwed.toFixed(1)}* (Pay to ${selectedPayer})\n\n`;
          }
        });

        payMsg += `-----------------------------------\n`;
        payMsg += `Thank you for ordering with PKC Food Delivery Dept! 🛵`;

        navigator.clipboard.writeText(payMsg).then(() => {
          showToast('Payment message copied to clipboard!', '💬');
        }).catch(() => {
          showToast('Failed to copy payment list.', '❌');
        });
      });

      changeTargetRestBtn.addEventListener('click', () => {
        showCustomDialog({
          title: "Change Target Restaurant",
          message: "Enter the restaurant name for this group order:",
          showInput: true,
          defaultValue: currentSelectedTargetRest,
          onConfirm: (val) => {
            if (val) {
              currentSelectedTargetRest = val;
              pushToCloud();
              renderGroupOrders();
              renderSplitterPage();
              showToast(`Target restaurant updated to ${val}`, '🍜');
            }
          }
        });
      });

      restaurantListEl.addEventListener('click', (e) => {
        const toggleCheckbox = e.target.closest('.toggle-active');
        if (toggleCheckbox) {
          const id = toggleCheckbox.getAttribute('data-id');
          const spot = restaurants.find(r => r.id === id);
          if (spot) {
            spot.active = toggleCheckbox.checked;
            pushToCloud();
            renderRestaurantList();
          }
          return;
        }

        const deleteBtn = e.target.closest('.delete-btn');
        if (deleteBtn) {
          const id = deleteBtn.getAttribute('data-id');
          restaurants = restaurants.filter(r => r.id !== id);
          pushToCloud();
          renderRestaurantList();
        }
      });

      document.getElementById('selectAllBtn').addEventListener('click', () => {
        restaurants.forEach(r => r.active = true);
        pushToCloud();
        renderRestaurantList();
      });

      document.getElementById('deselectAllBtn').addEventListener('click', () => {
        restaurants.forEach(r => r.active = false);
        pushToCloud();
        renderRestaurantList();
      });

      document.getElementById('clearAllBtn').addEventListener('click', () => {
        showCustomDialog({
          title: "Clear All Spots",
          message: "Are you sure you want to remove all restaurants from your list?",
          onConfirm: () => {
            restaurants = [];
            pushToCloud();
            renderRestaurantList();
            showToast('Cleared all restaurant options.', '🗑️');
          }
        });
      });

      document.getElementById('preloadBtn').addEventListener('click', () => {
        const existingNames = new Set(restaurants.map(r => r.name.toLowerCase()));
        PRELOADED_RESTAURANTS.forEach(spot => {
          if (!existingNames.has(spot.name.toLowerCase())) {
            restaurants.push({ ...spot, id: Date.now() + Math.random().toString() });
          }
        });

        pushToCloud();
        renderRestaurantList();
        showToast('Pre-loaded Sha Tin & Tai Wai spots!', '📍');
      });

      document.getElementById('exportBtn').addEventListener('click', () => {
        if (restaurants.length === 0) {
          showToast('No spots available to export.', '⚠️');
          return;
        }
        const exportData = {
          roomCode: currentRoomCode,
          restaurants,
          groupOrders,
          targetRestaurant: currentSelectedTargetRest,
          selectedPayer,
          payerFpsInfo,
          splitMethod,
          extraCharges,
          extraDiscount,
          paidStatusMap
        };
        const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(exportData, null, 2));
        const downloadAnchor = document.createElement('a');
        downloadAnchor.setAttribute("href", dataStr);
        downloadAnchor.setAttribute("download", `Food_Delivery_Dept_${currentRoomCode}_${new Date().toISOString().slice(0,10)}.json`);
        document.body.appendChild(downloadAnchor);
        downloadAnchor.click();
        downloadAnchor.remove();
        showToast('Exported room data file!', '💾');
      });

      document.getElementById('startOrderFromModalBtn').addEventListener('click', () => {
        closeModal();
        switchTab('orders');
      });

      document.getElementById('closeModalBtn').addEventListener('click', closeModal);
      document.getElementById('spinAgainModalBtn').addEventListener('click', () => {
        closeModal();
        setTimeout(spinWheel, 350);
      });
    }

    // SAFE READY BINDING
    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', init);
    } else {
      init();
    }
  </script>
</body>
</html>