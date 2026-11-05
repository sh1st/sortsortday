<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>沙田及馬鞍山郵政分揀格 (STA1-STA64) 完整實戰特訓器</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;900&display=swap');
        
        body {
            font-family: 'Inter', system-ui, -apple-system, sans-serif;
            background-color: #0b132b;
            color: #e0e6ed;
            user-select: none;
        }

        /* Custom Scrollbars */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #1c2541;
        }
        ::-webkit-scrollbar-thumb {
            background: #3a506b;
            border-radius: 4px;
        }

        /* Grid Cell Styling */
        .grid-cell {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
        }
        
        .grid-cell.drag-over {
            background-color: #1f4068 !important;
            border-color: #00b4d8 !important;
            transform: scale(1.03);
            box-shadow: 0 0 15px rgba(0, 180, 216, 0.5);
            z-index: 10;
        }

        /* Success & Error Animations */
        @keyframes correctGlow {
            0% { background-color: #10b981; border-color: #34d399; transform: scale(1.05); }
            100% { background-color: #064e3b; border-color: #059669; transform: scale(1); }
        }

        @keyframes errorShake {
            0%, 100% { transform: translateX(0); }
            20%, 60% { transform: translateX(-6px); }
            40%, 80% { transform: translateX(6px); }
        }

        .cell-correct {
            animation: correctGlow 0.5s forwards;
        }

        .cell-error {
            animation: errorShake 0.4s forwards;
            border-color: #ef4444 !important;
            background-color: #7f1d1d !important;
        }

        /* Draggable Card Animation */
        .draggable-card {
            touch-action: none;
            cursor: grab;
            transition: transform 0.15s, box-shadow 0.15s;
        }
        .draggable-card:active {
            cursor: grabbing;
            transform: scale(1.05);
            box-shadow: 0 10px 25px rgba(0, 245, 212, 0.4);
        }
        .draggable-card.dragging {
            opacity: 0.5;
        }

        /* Glassmorphism Panel */
        .glass-panel {
            background: rgba(28, 37, 65, 0.85);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between overflow-x-hidden">

    <!-- Header & Dashboard Stats -->
    <header class="glass-panel sticky top-0 z-50 border-b border-slate-700/50 px-4 py-3 shadow-lg">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-4">
            
            <!-- Title & Badge -->
            <div class="flex items-center space-x-3">
                <div class="bg-cyan-500/10 border border-cyan-500/30 p-2.5 rounded-xl text-cyan-400">
                    <i class="fa-solid fa-boxes-packing text-2xl"></i>
                </div>
                <div>
                    <h1 class="font-black text-xl md:text-2xl text-white tracking-wide flex items-center gap-2">
                        沙田馬鞍山郵政分揀格 <span class="bg-cyan-500 text-slate-950 text-xs px-2 py-0.5 rounded font-bold uppercase">64格 PDF 實戰特訓器</span>
                    </h1>
                    <p class="text-xs text-slate-400">Postal Sorting Box Professional Trainer (STA1-STA64)</p>
                </div>
            </div>

            <!-- Mode Selector Buttons -->
            <div class="flex items-center bg-slate-900/80 p-1 rounded-xl border border-slate-700/60">
                <button id="mode-drag-btn" onclick="setGameMode('drag')" 
                    class="flex items-center gap-2 px-4 py-2 rounded-lg text-xs md:text-sm font-bold transition-all bg-cyan-500 text-slate-950 shadow-md">
                    <i class="fa-solid fa-hand-pointer"></i> 拖放/點擊實戰測驗
                </button>
                <button id="mode-flashcard-btn" onclick="setGameMode('flashcard')" 
                    class="flex items-center gap-2 px-4 py-2 rounded-lg text-xs md:text-sm font-bold text-slate-400 hover:text-white transition-all">
                    <i class="fa-solid fa-eye"></i> 自由開格盲測
                </button>
            </div>

            <!-- Live Performance Stats -->
            <div class="flex items-center gap-4 text-xs md:text-sm">
                <div class="bg-slate-900/60 border border-slate-700/50 px-3 py-1.5 rounded-lg flex items-center gap-2">
                    <i class="fa-solid fa-stopwatch text-amber-400"></i>
                    <span id="timer-display" class="font-mono font-bold text-amber-300 text-base">00:00</span>
                </div>
                <div class="bg-slate-900/60 border border-slate-700/50 px-3 py-1.5 rounded-lg flex items-center gap-2">
                    <i class="fa-solid fa-bullseye text-emerald-400"></i>
                    <span>正確率: <strong id="accuracy-display" class="text-emerald-400 text-base">100%</strong></span>
                </div>
                <div class="bg-slate-900/60 border border-slate-700/50 px-3 py-1.5 rounded-lg flex items-center gap-2">
                    <i class="fa-solid fa-trophy text-cyan-400"></i>
                    <span>得分: <strong id="score-display" class="text-cyan-400 text-base">0</strong></span>
                </div>
            </div>

        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-7xl mx-auto w-full px-2 sm:px-4 py-4 flex-grow flex flex-col gap-4">
        
        <!-- Interactive Game Deck Area (Moved to Top for Easy Play) -->
        <div id="deck-panel" class="glass-panel p-4 rounded-2xl border-cyan-500/30 shadow-2xl relative min-h-[120px] flex flex-col justify-center items-center">
            
            <!-- Drag Game Deck Content -->
            <div id="drag-deck-content" class="w-full flex flex-col md:flex-row items-center justify-between gap-4">
                <div class="text-center md:text-left">
                    <span class="text-xs uppercase tracking-widest text-cyan-400 font-bold block mb-0.5"><i class="fa-solid fa-envelope-open-text mr-1"></i> 請把以下郵件卡片拖放或點擊投遞至下方正確 STA 格子</span>
                    <h2 class="text-slate-400 text-xs">剩餘待分發郵件: <span id="cards-remaining" class="text-white font-bold text-sm">0</span> 格</h2>
                </div>

                <!-- Current Address Card to Drag -->
                <div id="active-card-container" class="flex-grow flex justify-center items-center py-1">
                    <!-- Dynamic Card Element -->
                </div>

                <div class="text-right hidden md:block">
                    <div class="text-xs text-slate-400">操作提示</div>
                    <div class="text-xs font-semibold text-slate-300">支援手機點選或電腦拖拽投放</div>
                </div>
            </div>

            <!-- Flashcard Mode Legend / Info Content -->
            <div id="flashcard-deck-content" class="hidden w-full text-center py-2">
                <h3 class="text-cyan-300 font-bold text-sm mb-1"><i class="fa-solid fa-lightbulb text-amber-400 mr-1.5"></i>自由開格盲測模式</h3>
                <p class="text-xs text-slate-300 max-w-xl mx-auto">
                    點擊下方 8x8 矩陣中任何一個 STA 格，即可檢視該格所對應的屋苑與地標清單。再度點擊可覆蓋格子進行自主默記盲測！
                </p>
            </div>

        </div>

        <!-- Controls & Zone Filter Bar -->
        <div class="glass-panel p-3 rounded-2xl flex flex-wrap items-center justify-between gap-3 text-xs md:text-sm">
            <div class="flex items-center gap-2 flex-wrap">
                <span class="text-slate-400 font-semibold"><i class="fa-solid fa-filter text-cyan-400 mr-1"></i>區域分區過濾:</span>
                <button onclick="filterZone('ALL')" class="zone-btn active-zone border border-cyan-500 bg-cyan-500/20 text-cyan-300 px-3 py-1 rounded-lg font-bold">全部 64 格</button>
                <button onclick="filterZone('TAI_WAI')" class="zone-btn border border-slate-700 hover:border-slate-500 px-3 py-1 rounded-lg text-slate-300">大圍/顯徑</button>
                <button onclick="filterZone('CITY_CENTER')" class="zone-btn border border-slate-700 hover:border-slate-500 px-3 py-1 rounded-lg text-slate-300">市中心/沙角</button>
                <button onclick="filterZone('FO_TAN')" class="zone-btn border border-slate-700 hover:border-slate-500 px-3 py-1 rounded-lg text-slate-300">火炭/中大/九肚山</button>
                <button onclick="filterZone('CITY_ONE')" class="zone-btn border border-slate-700 hover:border-slate-500 px-3 py-1 rounded-lg text-slate-300">第一城/石門</button>
                <button onclick="filterZone('MOS')" class="zone-btn border border-slate-700 hover:border-slate-500 px-3 py-1 rounded-lg text-slate-300">馬鞍山/烏溪沙</button>
            </div>

            <div class="flex items-center gap-2">
                <button onclick="resetGame()" class="bg-rose-500/20 hover:bg-rose-500/30 text-rose-300 border border-rose-500/40 px-3 py-1.5 rounded-lg font-bold transition-all flex items-center gap-1.5">
                    <i class="fa-solid fa-rotate-right"></i> 重設修練
                </button>
            </div>
        </div>

        <!-- 8x8 Sorting Grid Container -->
        <div class="glass-panel p-2 md:p-4 rounded-2xl shadow-2xl overflow-x-auto">
            <!-- 8 Column Grid Layout -->
            <div id="grid-matrix" class="grid grid-cols-8 gap-1.5 md:gap-2.5 min-w-[780px]">
                <!-- Dynamic 64 cells generated by JS -->
            </div>
        </div>

    </main>

    <!-- Footer -->
    <footer class="glass-panel border-t border-slate-800 py-3 text-center text-xs text-slate-500">
        沙田及馬鞍山郵政分揀格 (STA1-STA64) PDF 完整專業速記與實戰模擬器 &copy; 2026
    </footer>

    <!-- Result Modal -->
    <div id="result-modal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 flex items-center justify-center hidden opacity-0 transition-opacity duration-300">
        <div class="bg-slate-900 border border-cyan-500/40 p-6 md:p-8 rounded-3xl max-w-md w-full mx-4 shadow-2xl text-center flex flex-col items-center">
            <div class="w-16 h-16 bg-emerald-500/20 border border-emerald-500 text-emerald-400 rounded-full flex items-center justify-center text-3xl mb-4">
                <i class="fa-solid fa-circle-check"></i>
            </div>
            <h2 class="text-2xl font-black text-white mb-1">特訓圓滿完成！</h2>
            <p class="text-xs text-slate-400 mb-6">你已成功完成全部 64 格分揀實戰測驗</p>

            <div class="w-full bg-slate-950/60 rounded-2xl p-4 border border-slate-800 mb-6 flex justify-around text-center">
                <div>
                    <div class="text-xs text-slate-400">總耗時</div>
                    <div id="modal-time" class="text-lg font-mono font-bold text-amber-400">00:00</div>
                </div>
                <div class="border-r border-slate-800"></div>
                <div>
                    <div class="text-xs text-slate-400">正確率</div>
                    <div id="modal-accuracy" class="text-lg font-bold text-emerald-400">100%</div>
                </div>
                <div class="border-r border-slate-800"></div>
                <div>
                    <div class="text-xs text-slate-400">最終得分</div>
                    <div id="modal-score" class="text-lg font-bold text-cyan-400">0</div>
                </div>
            </div>

            <button onclick="resetGame(); closeModal()" class="w-full bg-cyan-500 hover:bg-cyan-400 text-slate-950 font-extrabold py-3 rounded-xl transition-all shadow-lg shadow-cyan-500/20">
                再次挑戰 / 再刷極限
            </button>
        </div>
    </div>

    <!-- Application JavaScript Logic -->
    <script>
        // 64 Grid Complete Dataset mapped precisely from official DLO SCL PDF
        const gridData = [
            // Row 1 (STA1 - STA8)
            { id: 1, sta: "STA1", name: "顯徑邨", desc: "嘉徑苑, 顯徑體育館", zone: "TAI_WAI", icon: "🏠" },
            { id: 2, sta: "STA2", name: "顯田村 / 仁安醫院", desc: "瑞峰花園, 嘉田苑", zone: "TAI_WAI", icon: "🏥" },
            { id: 3, sta: "STA3", name: "顯耀邨 / 名城1-3期", desc: "盛薈, 盛世", zone: "TAI_WAI", icon: "🏢" },
            { id: 4, sta: "STA4", name: "上下徑口村", desc: "雲疊花園, 宏福苑", zone: "TAI_WAI", icon: "🏡" },
            { id: 5, sta: "STA5", name: "文禮閣 / 溱岸8號", desc: "車公廟站", zone: "CITY_CENTER", icon: "🏢" },
            { id: 6, sta: "STA6", name: "隆亨邨", desc: "田心村, 隆亨市集", zone: "TAI_WAI", icon: "🏠" },
            { id: 7, sta: "STA7", name: "金獅花園1-2期", desc: "世界花園, 景田苑", zone: "TAI_WAI", icon: "🏢" },
            { id: 8, sta: "STA8", name: "新田圍邨", desc: "沙田頭村, 豐盛苑", zone: "TAI_WAI", icon: "🏠" },

            // Row 2 (STA9 - STA16)
            { id: 9, sta: "STA9", name: "新翠邨", desc: "張煌偉小學, 循理小學", zone: "TAI_WAI", icon: "🏠" },
            { id: 10, sta: "STA10", name: "博康邨 / 乙明邨", desc: "沙角街, 基督書院", zone: "CITY_CENTER", icon: "🏠" },
            { id: 11, sta: "STA11", name: "沙角邨 / 愉城苑", desc: "沙田圍站", zone: "CITY_CENTER", icon: "🚆" },
            { id: 12, sta: "STA12", name: "花園城 / 河畔花園", desc: "麗豪酒店, 基覺小學", zone: "CITY_CENTER", icon: "🏨" },
            { id: 13, sta: "STA13", name: "水泉澳邨", desc: "水泉澳商場, 嶺泉/山泉", zone: "CITY_CENTER", icon: "⛰️" },
            { id: 14, sta: "STA14", name: "亞公角街", desc: "沙田醫院, 慈氏護養院", zone: "MOS", icon: "🏥" },
            { id: 15, sta: "STA15", name: "富豪花園 / 威院 PWH", desc: "愉田苑, 第一城51-52座", zone: "CITY_CENTER", icon: "🏥" },
            { id: 16, sta: "STA16", name: "沙田圍新村 ★", desc: "特別預留格/謝屋村", zone: "CITY_CENTER", icon: "★" },

            // Row 3 (STA17 - STA24)
            { id: 17, sta: "STA17", name: "大埔道 / 琵琶山", desc: "爾登豪庭, 寶福紀念館", zone: "TAI_WAI", icon: "🛣️" },
            { id: 18, sta: "STA18", name: "美田邨", desc: "美盈苑, 翠景花園", zone: "TAI_WAI", icon: "🏠" },
            { id: 19, sta: "STA19", name: "廣源邨 / 好運中心", desc: "沙田中心, 黃耀南小學", zone: "CITY_CENTER", icon: "🛍️" },
            { id: 20, sta: "STA20", name: "廣林苑 / 康林苑", desc: "沙田循道衛理小學", zone: "CITY_ONE", icon: "🏡" },
            { id: 21, sta: "STA21", name: "恒生大學 / 帝堡城", desc: "綠怡雅苑, 小瀝源路", zone: "CITY_ONE", icon: "🎓" },
            { id: 22, sta: "STA22", name: "美林邨", desc: "曉翠山莊, 嘉御山, 美林體育館", zone: "TAI_WAI", icon: "🏠" },
            { id: 23, sta: "STA23", name: "柏傲莊 / 大圍站", desc: "圍方 The Wai, 富嘉花園", zone: "TAI_WAI", icon: "🚆" },
            { id: 24, sta: "STA24", name: "大圍村", desc: "積存街, 村南道, 金禧花園", zone: "TAI_WAI", icon: "🏘️" },

            // Row 4 (STA25 - STA32)
            { id: 25, sta: "STA25", name: "美松苑 / 壹號雲頂", desc: "美柏苑, 美城苑", zone: "TAI_WAI", icon: "🏔️" },
            { id: 26, sta: "STA26", name: "愉翠苑 (A-C座)", desc: "牛皮沙村, 插桅桿村", zone: "CITY_ONE", icon: "🏠" },
            { id: 27, sta: "STA27", name: "第一城 (1-20座)", desc: "置富第一城, 樂薈", zone: "CITY_ONE", icon: "🏢" },
            { id: 28, sta: "STA28", name: "第一城 (21-50座)", desc: "第一城街市, 匯豐/恒生", zone: "CITY_ONE", icon: "🏢" },
            { id: 29, sta: "STA29", name: "碧濤花園 1-3期", desc: "小瀝源路工業區, 永得利", zone: "CITY_ONE", icon: "🏭" },
            { id: 30, sta: "STA30", name: "石門商業區", desc: "京瑞廣場, 企業中心", zone: "CITY_ONE", icon: "🏢" },
            { id: 31, sta: "STA31", name: "翠湖花園", desc: "安景街, 京瑞廣場2期", zone: "CITY_ONE", icon: "🏡" },
            { id: 32, sta: "STA32", name: "碩門邨 / 石門站", desc: "愉德苑, 安睦街", zone: "CITY_ONE", icon: "🚆" },

            // Row 5 (STA33 - STA40)
            { id: 33, sta: "STA33", name: "九肚山 / 麗坪路", desc: "名日·九肚山, 駿景", zone: "FO_TAN", icon: "🏔️" },
            { id: 34, sta: "STA34", name: "瀝源邨", desc: "新城市廣場 3期, 帝都酒店", zone: "CITY_CENTER", icon: "🛍️" },
            { id: 35, sta: "STA35", name: "香港中文大學", desc: "中大醫院, 聯合書院, 凱悅", zone: "FO_TAN", icon: "🎓" },
            { id: 36, sta: "STA36", name: "排頭村 / 寶福山", desc: "萬佛寺, 新城市中央廣場", zone: "CITY_CENTER", icon: "⛩️" },
            { id: 37, sta: "STA37", name: "禾輋邨", desc: "沙田警署, 豐順街", zone: "CITY_CENTER", icon: "🏠" },
            { id: 38, sta: "STA38", name: "穗禾苑 / 沙田政府合署", desc: "翠華花園, 華翠園", zone: "FO_TAN", icon: "🏛️" },
            { id: 39, sta: "STA39", name: "銀禧花園 / 火炭站", desc: "樂景街, 御龍山", zone: "FO_TAN", icon: "🚆" },
            { id: 40, sta: "STA40", name: "御龍山 / 星堤岸", desc: "禾香街工業區, 稻苗學院", zone: "FO_TAN", icon: "🏢" },

            // Row 6 (STA41 - STA48)
            { id: 41, sta: "STA41", name: "錦駿苑 / 欣安邨", desc: "馬鞍山路, 德信中學", zone: "MOS", icon: "🏠" },
            { id: 42, sta: "STA42", name: "禾輋邨 (瑞和樓)", desc: "上·下禾輋村, 瀝源街", zone: "CITY_CENTER", icon: "🏡" },
            { id: 43, sta: "STA43", name: "晉名峰 / 坳背灣街", desc: "華樂工業中心, 威力工業", zone: "FO_TAN", icon: "🏭" },
            { id: 44, sta: "STA44", name: "穗禾苑 / 穗禾路", desc: "火炭村, 登利工業中心", zone: "FO_TAN", icon: "🏭" },
            { id: 45, sta: "STA45", name: "駿洋邨 / 山尾街", desc: "彩禾苑, 國際工業中心", zone: "FO_TAN", icon: "🏭" },
            { id: 46, sta: "STA46", name: "富安花園", desc: "大水坑, 崇真中學", zone: "MOS", icon: "🏠" },
            { id: 47, sta: "STA47", name: "頌安邨 / 福安花園", desc: "雅濤居, 迎濤灣, 海澄軒", zone: "MOS", icon: "🏠" },
            { id: 48, sta: "STA48", name: "恆安邨", desc: "馬鞍山運動場, 青年會書院", zone: "MOS", icon: "⚽" },

            // Row 7 (STA49 - STA56)
            { id: 49, sta: "STA49", name: "嵐岸 / 曉峰灣畔", desc: "沃泰街, 駿苑", zone: "MOS", icon: "🌊" },
            { id: 50, sta: "STA50", name: "亞公角漁民新村", desc: "烏溪沙青年新村, 泥涌", zone: "MOS", icon: "🌊" },
            { id: 51, sta: "STA51", name: "錦泰苑 / 大水坑站", desc: "大水坑村, 寧泰路", zone: "MOS", icon: "🚆" },
            { id: 52, sta: "STA52", name: "錦豐苑 / 頌安邨", desc: "頌和樓, 頌平樓", zone: "MOS", icon: "🏠" },
            { id: 53, sta: "STA53", name: "錦英苑", desc: "雅景台, 馬鞍山警署", zone: "MOS", icon: "🚓" },
            { id: 54, sta: "STA54", name: "銀湖天峰", desc: "翠擁華庭, 雅景台", zone: "MOS", icon: "🚆" },
            { id: 55, sta: "STA55", name: "聽濤雅苑 / 恆安站", desc: "錦暉苑, 博士山幼稚園", zone: "MOS", icon: "🚆" },
            { id: 56, sta: "STA56", name: "錦龍苑 / 利安邨", desc: "烏溪沙路口, 富寶花園", zone: "MOS", icon: "🏠" },

            // Row 8 (STA57 - STA64)
            { id: 57, sta: "STA57", name: "富寶花園", desc: "雅典居, 錦英路", zone: "MOS", icon: "🏢" },
            { id: 58, sta: "STA58", name: "耀安邨 / 錦禧苑", desc: "恆康街, 馮堯敬紀念中學", zone: "MOS", icon: "🏠" },
            { id: 59, sta: "STA59", name: "迎海 (1-5期)", desc: "新港城 1-4期, 烏溪沙村", zone: "MOS", icon: "🛍️" },
            { id: 60, sta: "STA60", name: "星漣海 / 雲海", desc: "泓碧, 迎海御峰, 泓碧", zone: "MOS", icon: "🌊" },
            { id: 61, sta: "STA61", name: "天宇海 / 啟新書院", desc: "海典灣, 觀瀾雅軒", zone: "MOS", icon: "🏫" },
            { id: 62, sta: "STA62", name: "海柏花園 / 馬鞍山站", desc: "MOSTown 新港城中心", zone: "MOS", icon: "🚆" },
            { id: 63, sta: "STA63", name: "海典灣 / 星濤灣", desc: "馬鞍山公園, 泳池", zone: "MOS", icon: "🏢" },
            { id: 64, sta: "STA64", name: "BOX (預留空格)", desc: "郵政分配預留分揀格", zone: "MOS", icon: "📦" }
        ];

        // State Variables
        let currentMode = 'drag'; // 'drag' or 'flashcard'
        let currentFilter = 'ALL';
        let remainingDeck = [];
        let activeCard = null;
        let score = 0;
        let totalAttempts = 0;
        let correctAttempts = 0;
        let timerInterval = null;
        let secondsElapsed = 0;
        let isTimerRunning = false;

        // Initialize App
        window.onload = function() {
            renderGrid();
            resetGame();
        };

        // Render 8x8 Grid
        function renderGrid() {
            const gridContainer = document.getElementById('grid-matrix');
            gridContainer.innerHTML = '';

            gridData.forEach(item => {
                const cell = document.createElement('div');
                cell.id = `cell-${item.id}`;
                cell.dataset.staId = item.id;
                cell.className = `grid-cell glass-panel rounded-xl p-2 md:p-2.5 flex flex-col justify-between border border-slate-700/60 hover:border-cyan-500/50 cursor-pointer h-20 md:h-24 ${getZoneBg(item.zone)}`;
                
                cell.innerHTML = `
                    <div class="flex items-center justify-between pointer-events-none">
                        <span class="text-[10px] md:text-xs font-black bg-slate-950 text-cyan-400 px-1.5 py-0.5 rounded border border-cyan-500/30">${item.sta}</span>
                        <span class="text-xs md:text-sm">${item.icon}</span>
                    </div>
                    <div id="cell-content-${item.id}" class="cell-body text-center my-auto pointer-events-none">
                        <div class="text-[11px] md:text-xs font-bold text-slate-200 truncate leading-tight">${item.name}</div>
                        <div class="text-[9px] md:text-[10px] text-slate-400 truncate mt-0.5">${item.desc}</div>
                    </div>
                `;

                // Drag & Drop event listeners
                cell.addEventListener('dragover', handleDragOver);
                cell.addEventListener('dragleave', handleDragLeave);
                cell.addEventListener('drop', handleDrop);
                cell.addEventListener('click', () => handleCellClick(item));

                gridContainer.appendChild(cell);
            });
        }

        function getZoneBg(zone) {
            switch(zone) {
                case 'TAI_WAI': return 'hover:bg-amber-500/10';
                case 'CITY_CENTER': return 'hover:bg-yellow-500/10';
                case 'FO_TAN': return 'hover:bg-emerald-500/10';
                case 'CITY_ONE': return 'hover:bg-cyan-500/10';
                case 'MOS': return 'hover:bg-rose-500/10';
                default: return '';
            }
        }

        // Set Game Mode
        function setGameMode(mode) {
            currentMode = mode;
            const dragBtn = document.getElementById('mode-drag-btn');
            const flashcardBtn = document.getElementById('mode-flashcard-btn');
            const dragDeck = document.getElementById('drag-deck-content');
            const flashcardDeck = document.getElementById('flashcard-deck-content');

            if (mode === 'drag') {
                dragBtn.className = "flex items-center gap-2 px-4 py-2 rounded-lg text-xs md:text-sm font-bold transition-all bg-cyan-500 text-slate-950 shadow-md";
                flashcardBtn.className = "flex items-center gap-2 px-4 py-2 rounded-lg text-xs md:text-sm font-bold text-slate-400 hover:text-white transition-all";
                dragDeck.classList.remove('hidden');
                flashcardDeck.classList.add('hidden');
            } else {
                flashcardBtn.className = "flex items-center gap-2 px-4 py-2 rounded-lg text-xs md:text-sm font-bold transition-all bg-cyan-500 text-slate-950 shadow-md";
                dragBtn.className = "flex items-center gap-2 px-4 py-2 rounded-lg text-xs md:text-sm font-bold text-slate-400 hover:text-white transition-all";
                flashcardDeck.classList.remove('hidden');
                dragDeck.classList.add('hidden');
            }
            resetGame();
        }

        // Filter Grid Zone
        function filterZone(zoneKey) {
            currentFilter = zoneKey;
            
            document.querySelectorAll('.zone-btn').forEach(btn => {
                btn.className = "zone-btn border border-slate-700 hover:border-slate-500 px-3 py-1 rounded-lg text-slate-400";
            });
            event.target.className = "zone-btn active-zone border border-cyan-500 bg-cyan-500/20 text-cyan-300 px-3 py-1 rounded-lg font-bold";

            gridData.forEach(item => {
                const cell = document.getElementById(`cell-${item.id}`);
                if (zoneKey === 'ALL' || item.zone === zoneKey) {
                    cell.style.opacity = '1';
                    cell.style.pointerEvents = 'auto';
                } else {
                    cell.style.opacity = '0.25';
                    cell.style.pointerEvents = 'none';
                }
            });

            resetGame();
        }

        // Start / Reset Game Session
        function resetGame() {
            stopTimer();
            secondsElapsed = 0;
            score = 0;
            totalAttempts = 0;
            correctAttempts = 0;
            updateStats();

            let filteredData = currentFilter === 'ALL' 
                ? [...gridData] 
                : gridData.filter(d => d.zone === currentFilter);

            remainingDeck = shuffleArray(filteredData);

            gridData.forEach(item => {
                const content = document.getElementById(`cell-content-${item.id}`);
                const cell = document.getElementById(`cell-${item.id}`);
                cell.classList.remove('cell-correct', 'cell-error');

                if (currentMode === 'drag') {
                    content.style.visibility = 'hidden';
                } else {
                    content.style.visibility = 'visible';
                }
            });

            loadNextCard();
        }

        // Load next active card to drag
        function loadNextCard() {
            const cardContainer = document.getElementById('active-card-container');
            document.getElementById('cards-remaining').innerText = remainingDeck.length;

            if (remainingDeck.length === 0) {
                cardContainer.innerHTML = `<span class="text-emerald-400 font-bold text-sm"><i class="fa-solid fa-flag-checkered mr-1"></i> 全部 64 格分揀完成！</span>`;
                stopTimer();
                showModal();
                return;
            }

            activeCard = remainingDeck[0];

            cardContainer.innerHTML = `
                <div id="draggable-card" draggable="true" 
                    class="draggable-card bg-slate-900 border-2 border-cyan-400/80 px-6 py-3 rounded-2xl shadow-xl flex items-center gap-3 text-white max-w-sm w-full">
                    <span class="text-2xl">${activeCard.icon}</span>
                    <div class="flex-grow">
                        <div class="text-xs text-cyan-400 font-bold">請派遞信件至目標格...</div>
                        <div class="font-black text-base md:text-lg text-white tracking-wide">${activeCard.name}</div>
                        <div class="text-xs text-slate-400 truncate">${activeCard.desc}</div>
                    </div>
                    <i class="fa-solid fa-grip-vertical text-slate-500 text-lg"></i>
                </div>
            `;

            const cardEl = document.getElementById('draggable-card');
            cardEl.addEventListener('dragstart', handleDragStart);
            cardEl.addEventListener('dragend', handleDragEnd);
        }

        // Drag & Drop Handlers
        function handleDragStart(e) {
            startTimer();
            e.dataTransfer.setData('text/plain', activeCard.id);
            this.classList.add('dragging');
        }

        function handleDragEnd() {
            this.classList.remove('dragging');
        }

        function handleDragOver(e) {
            e.preventDefault();
            this.classList.add('drag-over');
        }

        function handleDragLeave() {
            this.classList.remove('drag-over');
        }

        function handleDrop(e) {
            e.preventDefault();
            this.classList.remove('drag-over');
            const targetStaId = parseInt(this.dataset.staId);
            verifyAnswer(targetStaId, this);
        }

        // Verify Answer
        function verifyAnswer(targetStaId, cellElement) {
            if (!activeCard || currentMode !== 'drag') return;

            totalAttempts++;

            if (targetStaId === activeCard.id) {
                correctAttempts++;
                score += 100;
                cellElement.classList.add('cell-correct');
                
                const content = document.getElementById(`cell-content-${targetStaId}`);
                content.style.visibility = 'visible';

                remainingDeck.shift();
                setTimeout(() => {
                    cellElement.classList.remove('cell-correct');
                    loadNextCard();
                }, 300);

            } else {
                score = Math.max(0, score - 30);
                cellElement.classList.add('cell-error');
                setTimeout(() => {
                    cellElement.classList.remove('cell-error');
                }, 500);
            }

            updateStats();
        }

        // Handle Click for Flashcard & Tap interaction
        function handleCellClick(item) {
            if (currentMode === 'flashcard') {
                const content = document.getElementById(`cell-content-${item.id}`);
                content.style.visibility = content.style.visibility === 'hidden' ? 'visible' : 'hidden';
            } else if (currentMode === 'drag' && activeCard) {
                const cellElement = document.getElementById(`cell-${item.id}`);
                verifyAnswer(item.id, cellElement);
            }
        }

        function updateStats() {
            document.getElementById('score-display').innerText = score;
            const accuracy = totalAttempts === 0 ? 100 : Math.round((correctAttempts / totalAttempts) * 100);
            document.getElementById('accuracy-display').innerText = `${accuracy}%`;
        }

        function startTimer() {
            if (isTimerRunning) return;
            isTimerRunning = true;
            timerInterval = setInterval(() => {
                secondsElapsed++;
                const mins = String(Math.floor(secondsElapsed / 60)).padStart(2, '0');
                const secs = String(secondsElapsed % 60).padStart(2, '0');
                document.getElementById('timer-display').innerText = `${mins}:${secs}`;
            }, 1000);
        }

        function stopTimer() {
            clearInterval(timerInterval);
            isTimerRunning = false;
        }

        function shuffleArray(array) {
            const arr = [...array];
            for (let i = arr.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [arr[i], arr[j]] = [arr[j], arr[i]];
            }
            return arr;
        }

        function showModal() {
            const modal = document.getElementById('result-modal');
            document.getElementById('modal-time').innerText = document.getElementById('timer-display').innerText;
            document.getElementById('modal-accuracy').innerText = document.getElementById('accuracy-display').innerText;
            document.getElementById('modal-score').innerText = score;

            modal.classList.remove('hidden');
            setTimeout(() => modal.classList.remove('opacity-0'), 10);
        }

        function closeModal() {
            const modal = document.getElementById('result-modal');
            modal.classList.add('opacity-0');
            setTimeout(() => modal.classList.add('hidden'), 300);
        }
    </script>
</body>
</html>
