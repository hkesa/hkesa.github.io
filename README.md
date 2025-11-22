<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Horizon Employment Agency</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <!-- Firebase SDK -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
        import { getFirestore, collection, addDoc, getDocs, doc, updateDoc, deleteDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore.js";

        // ▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼
        // 【設定區域】請在此貼上您的 Firebase Config
        // ▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼▼
        const firebaseConfig = {
            apiKey: "AIzaSyDxxxxxxxxxxxxxxxxxxxxxxxx", 
            authDomain: "your-project.firebaseapp.com",
            projectId: "your-project-id",
            storageBucket: "your-project.appspot.com",
            messagingSenderId: "123456789",
            appId: "1:123456789:web:xxxxxxxxxxx"
        };
        // ▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲▲

        window.isFirebaseMode = false;
        window.db = null;
        window.collection = collection;
        window.addDoc = addDoc;
        window.doc = doc;
        window.updateDoc = updateDoc;
        window.deleteDoc = deleteDoc;
        window.onSnapshot = onSnapshot;
        window.query = query;

        async function initSystem() {
            const isConfigured = firebaseConfig.projectId !== "your-project-id" && !firebaseConfig.apiKey.includes("xxxx");
            if (isConfigured) {
                try {
                    const app = initializeApp(firebaseConfig);
                    window.db = getFirestore(app);
                    window.isFirebaseMode = true;
                    console.log("Firebase Mode");
                    document.getElementById('mode-badge').innerHTML = '<span class="bg-green-100 text-green-800 text-xs font-medium mr-2 px-2 py-0.5 rounded border border-green-400">雲端同步模式 (Firebase)</span>';
                } catch (e) {
                    console.error(e);
                    activateOfflineMode();
                }
            } else {
                activateOfflineMode();
            }
            window.initApp();
        }

        function activateOfflineMode() {
            window.isFirebaseMode = false;
            console.log("Offline Mode");
            document.getElementById('mode-badge').innerHTML = '<span class="bg-orange-100 text-orange-800 text-xs font-medium mr-2 px-2 py-0.5 rounded border border-orange-400">單機模式 (資料僅存於此裝置)</span>';
            const toast = document.getElementById('offline-toast');
            toast.classList.remove('hidden');
            setTimeout(() => { toast.classList.add('opacity-0'); setTimeout(()=>toast.remove(), 1000); }, 5000);
        }
        initSystem();
    </script>

    <style>
        /* 字體大小設定 (回復至較小尺寸) */
        html { font-size: 16px; } 
        body { font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; background-color: #f3f4f6; }
        .btn-nav.active { background-color: #1e40af; color: white; }
        .hidden-section { display: none; }
        
        .wa-btn {
            background-color: #25D366; color: white;
            font-weight: bold; padding: 10px 20px; border-radius: 50px;
            display: inline-flex; align-items: center; gap: 8px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1); transition: transform 0.2s;
            text-decoration: none; font-size: 1rem;
        }
        .wa-btn:hover { transform: scale(1.05); background-color: #1ebc57; }

        #loading-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(255,255,255,0.9); z-index: 9999;
            display: flex; justify-content: center; align-items: center;
            color: #1e40af; transition: opacity 0.5s;
        }
        
        /* 隱藏數字輸入框的預設箭頭 (Spinner) */
        input::-webkit-outer-spin-button,
        input::-webkit-inner-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type=number] {
            -moz-appearance: textfield; /* Firefox */
        }

        /* Custom Styles */
        input, select, textarea { font-size: 0.95rem; }
        .promo-box {
            border-radius: 8px; padding: 12px; margin-bottom: 15px; text-align: center;
            font-weight: bold; font-size: 0.9rem; line-height: 1.5;
        }
        .salary-btn {
            background-color: #e5e7eb; color: #374151;
            width: 50px; display: flex; align-items: center; justify-content: center;
            cursor: pointer; user-select: none; font-size: 1.2rem; font-weight: bold;
        }
        .salary-btn:hover { background-color: #d1d5db; }
        .salary-btn:active { background-color: #9ca3af; }
    </style>
</head>
<body class="pb-20 text-gray-800">

    <div id="loading-overlay">
        <div class="text-center">
            <i class="fa-solid fa-circle-notch fa-spin text-4xl mb-4"></i><br>
            <span class="text-xl font-bold">系統啟動中...</span>
        </div>
    </div>

    <div id="offline-toast" class="hidden fixed bottom-5 right-5 bg-white border-l-4 border-orange-500 text-gray-700 p-4 rounded shadow-lg z-50 transition-opacity duration-1000 max-w-xs text-sm">
        <p class="font-bold"><i class="fa-solid fa-triangle-exclamation text-orange-500"></i> 目前為單機模式</p>
        <p class="mt-1">尚未設定 Firebase 金鑰，資料僅儲存於此。</p>
    </div>

    <!-- 導航 -->
    <nav class="bg-white shadow-md sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4">
            <div class="flex flex-col md:flex-row justify-between items-center py-3">
                <div class="flex flex-col items-center md:items-start mb-2 md:mb-0">
                    <div class="font-bold text-xl text-blue-900 text-center md:text-left">
                        Horizon Employment Agency
                    </div>
                    <div id="mode-badge" class="mt-1"></div>
                </div>
                <div class="flex space-x-2">
                    <button onclick="switchTab('admin')" id="nav-admin" class="btn-nav px-4 py-2 rounded text-sm font-medium text-gray-700 hover:bg-blue-50 transition">
                        🇭🇰 管理員
                    </button>
                    <button onclick="switchTab('english')" id="nav-english" class="btn-nav px-4 py-2 rounded text-sm font-medium text-gray-700 hover:bg-blue-50 transition">
                        🇵🇭 English
                    </button>
                    <button onclick="switchTab('indonesian')" id="nav-indonesian" class="btn-nav px-4 py-2 rounded text-sm font-medium text-gray-700 hover:bg-blue-50 transition">
                        🇮🇩 Indonesian
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- 1. 登入頁面 -->
    <section id="page-login" class="max-w-md mx-auto mt-10 p-8 bg-white rounded-lg shadow-lg hidden-section">
        <div class="text-center mb-6">
            <h2 class="text-xl font-bold text-gray-800">管理員登入 Administrator Login</h2>
            <p class="text-gray-500 text-sm mt-2">Please enter password to continue</p>
        </div>
        <div class="mb-6">
            <input type="password" id="admin-password" class="w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none text-base" placeholder="輸入密碼 Enter password">
            <p id="login-error" class="text-red-500 text-sm mt-2 hidden">密碼錯誤 / Incorrect Password</p>
        </div>
        <button onclick="checkLogin()" class="w-full bg-blue-600 text-white py-3 rounded-lg hover:bg-blue-700 font-bold text-base transition shadow">登入 Login</button>
    </section>

    <!-- 2. 管理員後台 -->
    <section id="page-admin" class="max-w-5xl mx-auto mt-6 px-4 hidden-section">
        <!-- 子選單 -->
        <div class="flex flex-wrap gap-3 mb-6 border-b pb-4">
            <button onclick="switchAdminSub('input')" class="px-4 py-2 rounded-full bg-blue-100 text-blue-800 font-bold text-sm hover:bg-blue-200 transition"><i class="fa-solid fa-pen mr-2"></i> 新資料輸入器</button>
            <button onclick="switchAdminSub('fishing')" class="px-4 py-2 rounded-full bg-purple-100 text-purple-800 font-bold text-sm hover:bg-purple-200 transition"><i class="fa-solid fa-fish mr-2"></i> 釣魚盤</button>
            <button onclick="switchAdminSub('list')" class="px-4 py-2 rounded-full bg-yellow-100 text-yellow-800 font-bold text-sm hover:bg-yellow-200 transition"><i class="fa-solid fa-list mr-2"></i> 已上載的資料</button>
        </div>

        <!-- 輸入區域 -->
        <div id="admin-input-area" class="bg-white p-6 rounded-xl shadow-md">
            <div id="fishing-header" class="hidden mb-6 bg-purple-50 p-4 rounded-lg border border-purple-200 flex justify-between items-center">
                <span class="font-bold text-purple-800 text-base">🎣 釣魚盤模式 (隨機生成)</span>
                <button onclick="generateFishingData()" class="bg-purple-600 text-white px-4 py-2 rounded font-bold hover:bg-purple-700 transition text-sm">重新生成數據</button>
            </div>

            <form id="employer-form" class="space-y-5">
                <input type="hidden" id="edit-doc-id"> 
                
                <!-- 僱主編號 -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">僱主編號 (Employer Number)</label>
                    <input type="text" id="inp-id" class="p-3 border rounded-lg w-full bg-gray-50" placeholder="例如：E2800">
                </div>

                <!-- 地區 -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">僱主地區 (Employer Location)</label>
                    <input type="text" id="inp-location" class="p-3 border rounded-lg w-full" placeholder="例如：將軍澳">
                </div>

                <!-- 國籍 -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">國籍 (Nationality)</label>
                    <select id="inp-nationality" class="p-3 border rounded-lg w-full" onchange="toggleNatOther()">
                        <option value="" disabled selected hidden>請選擇...</option>
                        <option value="Filipino">菲律賓 Filipino</option>
                        <option value="Indonesian">印尼 Indonesian</option>
                        <option value="Filipino & Indonesian">菲印 Filipino & Indonesian</option>
                        <option value="Indonesian & Filipino">印菲 Indonesian & Filipino</option>
                        <option value="Other">其他 (Other)</option>
                    </select>
                    <input type="text" id="inp-nationality-other" class="mt-2 p-3 border rounded-lg w-full hidden" placeholder="請以英文填寫，例如：Sri Lankans">
                </div>

                <!-- 家庭成員 -->
                <div class="flex flex-col p-4 bg-gray-50 rounded-lg border">
                    <label class="font-bold text-gray-700 mb-2 text-base">家庭成員及年齡 (Employer Member & Age)</label>
                    <div id="member-list-display" class="mb-2 flex flex-wrap gap-2 min-h-[30px]"></div>
                    <input type="hidden" id="inp-members-string">
                    
                    <div class="flex gap-2 items-end">
                        <div class="flex-1">
                            <label class="text-xs text-gray-500 mb-1 block">成員類型</label>
                            <select id="mem-type" class="w-full p-3 border rounded-lg" onchange="toggleAgeInput()">
                                <option value="成人">成人 Adult</option>
                                <option value="小孩">小孩 Child</option>
                                <option value="長者">長者 Elderly</option>
                                <option value="男孩">男孩 Boy</option>
                                <option value="女孩">女孩 Girl</option>
                                <option value="婆婆">婆婆 Grandma</option>
                                <option value="公公">公公 Grandpa</option>
                            </select>
                        </div>
                        <div class="w-24 hidden" id="age-input-group">
                            <label class="text-xs text-gray-500 mb-1 block">年齡</label>
                            <input type="number" id="mem-age" class="w-full p-3 border rounded-lg" placeholder="歲">
                        </div>
                        <button type="button" onclick="addMember()" class="bg-blue-600 text-white px-4 py-3 rounded-lg font-bold hover:bg-blue-700 text-sm h-full">新增</button>
                    </div>
                </div>

                <!-- 寵物 (列表模式) -->
                <div class="flex flex-col p-4 bg-gray-50 rounded-lg border">
                    <label class="font-bold text-gray-700 mb-2 text-base">寵物 (Pets)</label>
                    <div id="pet-list-display" class="mb-2 flex flex-wrap gap-2 min-h-[30px]"></div>
                    <input type="hidden" id="inp-pets-string">
                    
                    <div class="flex gap-2 items-end flex-wrap">
                        <div class="flex-1 min-w-[180px]">
                            <label class="text-xs text-gray-500 mb-1 block">寵物類型</label>
                            <select id="pet-add-type" class="w-full p-3 border rounded-lg" onchange="togglePetInputs()">
                                <option value="" disabled selected hidden>請選擇...</option>
                                <option value="沒有寵物">沒有寵物</option>
                                <option value="小狗">小狗 Small Dog</option>
                                <option value="中狗">中狗 Medium Dog</option>
                                <option value="大狗">大狗 Big Dog</option>
                                <option value="貓">貓 Cat</option>
                                <option value="兔">兔 Rabbit</option>
                                <option value="鸚鵡">鸚鵡 Parrot</option>
                                <option value="烏龜">烏龜 Turtle</option>
                                <option value="其他">其他 Other</option>
                            </select>
                        </div>
                        <!-- 其他文字輸入 -->
                        <div id="pet-other-group" class="w-full hidden order-last mt-2">
                            <input type="text" id="inp-pets-other-text" class="p-3 border rounded-lg w-full" placeholder="請以英文填寫，例如：Hamster">
                        </div>

                        <div class="w-36 hidden" id="pet-qty-group">
                             <label class="text-xs text-gray-500 mb-1 block">數量</label>
                             <div class="flex border rounded-lg overflow-hidden bg-white h-[48px]">
                                 <input type="number" id="pet-add-qty" class="w-full p-2 text-center border-none outline-none text-lg" value="1" min="1">
                                 <div class="flex gap-px pr-px items-center bg-gray-100">
                                     <button type="button" onclick="adjPet(1)" class="w-8 h-full bg-gray-200 hover:bg-gray-300 text-sm font-bold">▲</button>
                                     <button type="button" onclick="adjPet(-1)" class="w-8 h-full bg-gray-200 hover:bg-gray-300 text-sm font-bold">▼</button>
                                 </div>
                             </div>
                        </div>
                        <button type="button" onclick="addPetToList()" class="bg-blue-600 text-white px-4 py-3 rounded-lg font-bold hover:bg-blue-700 text-sm h-[48px]">新增</button>
                    </div>
                </div>

                <!-- 工人房 -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">工人房 (Helper Room)</label>
                    <select id="inp-room" class="p-3 border rounded-lg w-full" onchange="toggleRoomOther()">
                        <option value="" disabled selected hidden>請選擇...</option>
                        <option value="有工人房">有工人房</option>
                        <option value="與小孩同房">與小孩同房</option>
                        <option value="與長者同房">與長者同房</option>
                        <option value="與另外傭同房">與另外傭同房</option>
                        <option value="與女僱主同房">與女僱主同房</option>
                        <option value="其他">其他 (手寫)</option>
                    </select>
                    <input type="text" id="inp-room-other" class="mt-2 p-3 border rounded-lg w-full hidden" placeholder="請以英文填寫">
                </div>

                <!-- 上班日期 -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">上班日期 (Start Date)</label>
                    <select id="inp-date" class="p-3 border rounded-lg w-full">
                        <option value="" disabled selected hidden>請選擇...</option>
                        <option value="盡快">盡快</option>
                        <option value="一月">一月</option>
                        <option value="二月">二月</option>
                        <option value="三月">三月</option>
                        <option value="四月">四月</option>
                        <option value="五月">五月</option>
                        <option value="六月">六月</option>
                        <option value="七月">七月</option>
                        <option value="八月">八月</option>
                        <option value="九月">九月</option>
                        <option value="十月">十月</option>
                        <option value="十一月">十一月</option>
                        <option value="十二月">十二月</option>
                    </select>
                </div>

                <!-- 薪金 (獨立按鈕) -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">薪金 (Salary)</label>
                    <div class="flex items-center gap-3">
                        <input type="number" id="inp-salary" class="p-3 border rounded-lg flex-1 text-lg" placeholder="例如：5100">
                        <div class="flex gap-1">
                            <div onclick="adjustSalary(-100)" class="salary-btn rounded-l-lg border border-gray-300">▼</div>
                            <div onclick="adjustSalary(100)" class="salary-btn rounded-r-lg border border-gray-300">▲</div>
                        </div>
                    </div>
                </div>

                <!-- 語言 -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">語言要求 (Language)</label>
                    <select id="inp-lang" class="p-3 border rounded-lg w-full">
                        <option value="" disabled selected hidden>請選擇...</option>
                        <option value="英文">英文</option>
                        <option value="廣東話">廣東話</option>
                        <option value="普通話">普通話</option>
                        <option value="英文&廣東話">英文&廣東話</option>
                        <option value="英文&普通話">英文&普通話</option>
                    </select>
                </div>

                <!-- 備註 -->
                <div class="flex flex-col">
                    <label class="font-bold text-gray-700 mb-1 text-base">備註 (Remarks)</label>
                    <textarea id="inp-remarks" class="p-3 border rounded-lg w-full h-24 text-base" placeholder="非必填寫，如需要填寫備註，請以英文填寫"></textarea>
                </div>

                <button type="button" onclick="handleSave()" id="btn-save" class="w-full bg-green-600 text-white font-bold py-4 rounded-xl hover:bg-green-700 mt-6 text-lg shadow transition">
                    <i class="fa-solid fa-upload mr-2"></i> <span id="btn-save-text">上載資料</span>
                </button>
            </form>
        </div>

        <!-- 列表區域 (詳細顯示) -->
        <div id="admin-list-area" class="hidden">
            <h3 class="font-bold text-2xl mb-6 text-gray-800">已上載的資料 <span id="db-status-text" class="text-base font-normal text-gray-500"></span></h3>
            <div id="admin-records-container" class="space-y-6"></div>
        </div>
    </section>

    <!-- 3. 公開頁面 -->
    <section id="page-public" class="max-w-7xl mx-auto mt-8 px-4 hidden-section">
        
        <!-- WhatsApp Banner -->
        <div class="mb-6 flex flex-col items-center">
            <div id="banner-box" class="w-full max-w-2xl mb-4 p-4 rounded-lg text-center shadow-md border-l-8">
                <p id="banner-text" class="text-lg font-bold leading-relaxed"></p>
            </div>
            <a href="https://wa.me/85296111003" target="_blank" class="wa-btn">
                <i class="fa-brands fa-whatsapp text-3xl"></i> <span id="wa-text">WhatsApp 9611 1003</span>
            </a>
        </div>

        <h2 id="public-title" class="text-3xl font-bold text-blue-900 mb-6 border-l-8 border-blue-500 pl-4">Employer List</h2>

        <div id="public-records-container" class="grid grid-cols-1 md:grid-cols-2 gap-8"></div>

        <!-- 分頁 -->
        <div id="pagination-controls" class="flex justify-center items-center gap-6 mt-10 mb-12 text-lg">
            <button onclick="changePage(-1)" id="btn-prev" class="px-6 py-3 bg-white border rounded-lg hover:bg-gray-100 font-bold shadow">Prev</button>
            <span id="page-indicator" class="font-bold text-gray-700">Page 1</span>
            <button onclick="changePage(1)" id="btn-next" class="px-6 py-3 bg-white border rounded-lg hover:bg-gray-100 font-bold shadow">Next</button>
        </div>
    </section>

    <script>
        let employers = [];
        const PASSWORD = 'admin@ADA2018';
        let currentPage = 1;
        const itemsPerPage = 10;
        let currentLang = 'english';
        const LOCAL_STORAGE_KEY = 'horizon_employers_offline_v3';

        // --- 翻譯與地圖 ---
        const locationMap = {
            "中環": "Central", "金鐘": "Admiralty", "灣仔": "Wan Chai", "銅鑼灣": "Causeway Bay", "北角": "North Point",
            "太古": "Tai Koo", "筲箕灣": "Shau Kei Wan", "柴灣": "Chai Wan", "尖沙咀": "Tsim Sha Tsui", "佐敦": "Jordan",
            "油麻地": "Yau Ma Tei", "旺角": "Mong Kok", "太子": "Prince Edward", "深水埗": "Sham Shui Po", "長沙灣": "Cheung Sha Wan",
            "荔枝角": "Lai Chi Kok", "美孚": "Mei Foo", "九龍塘": "Kowloon Tong", "黃大仙": "Wong Tai Sin", "鑽石山": "Diamond Hill",
            "彩虹": "Choi Hung", "九龍灣": "Kowloon Bay", "牛頭角": "Ngau Tau Kok", "觀塘": "Kwun Tong", "藍田": "Lam Tin",
            "調景嶺": "Tiu Keng Leng", "將軍澳": "Tseung Kwan O", "坑口": "Hang Hau", "寶琳": "Po Lam", "大圍": "Tai Wai",
            "沙田": "Sha Tin", "火炭": "Fo Tan", "大學": "University", "大埔墟": "Tai Po Market", "太和": "Tai Wo",
            "粉嶺": "Fanling", "上水": "Sheung Shui", "荃灣": "Tsuen Wan", "大窩口": "Tai Wo Hau", "葵興": "Kwai Hing",
            "葵芳": "Kwai Fong", "青衣": "Tsing Yi", "東涌": "Tung Chung", "元朗": "Yuen Long", "屯門": "Tuen Mun", "天水圍": "Tin Shui Wai"
        };

        const dict = {
            '有工人房': { en: 'Yes, have own room', id: 'Ya, ada kamar sendiri' },
            '與小孩同房': { en: 'Share room with kid', id: 'Berbagi kamar dengan anak' },
            '與長者同房': { en: 'Share room with elderly', id: 'Berbagi kamar dengan lansia' },
            '與另外傭同房': { en: 'Share with other helper', id: 'Berbagi dengan pembantu lain' },
            '與女僱主同房': { en: 'Share with female employer', id: 'Berbagi dengan majikan wanita' },
            '盡快': { en: 'ASAP', id: 'Secepatnya' },
            '一月': { en: 'January', id: 'Januari' }, '二月': { en: 'February', id: 'Februari' }, '三月': { en: 'March', id: 'Maret' },
            '四月': { en: 'April', id: 'April' }, '五月': { en: 'May', id: 'Mei' }, '六月': { en: 'June', id: 'Juni' },
            '七月': { en: 'July', id: 'Juli' }, '八月': { en: 'August', id: 'Agustus' }, '九月': { en: 'September', id: 'September' },
            '十月': { en: 'October', id: 'Oktober' }, '十一月': { en: 'November', id: 'November' }, '十二月': { en: 'December', id: 'Desember' },
            '廣東話': { en: 'Cantonese', id: 'Kantonis' }, '英文': { en: 'English', id: 'Inggris' }, '普通話': { en: 'Mandarin', id: 'Mandarin' },
            '英文&廣東話': { en: 'English & Cantonese', id: 'Inggris & Kantonis' }, '英文&普通話': { en: 'English & Mandarin', id: 'Inggris & Mandarin' },
            // Pets
            '小狗': { en: 'Small Dog', id: 'Anjing Kecil' }, '中狗': { en: 'Medium Dog', id: 'Anjing Sedang' }, '大狗': { en: 'Big Dog', id: 'Anjing Besar' },
            '貓': { en: 'Cat', id: 'Kucing' }, '兔': { en: 'Rabbit', id: 'Kelinci' },
            '鸚鵡': { en: 'Parrot', id: 'Burung Beo' }, '烏龜': { en: 'Turtle', id: 'Kura-kura' },
            '其他': { en: 'Other', id: 'Lainnya' }, '沒有寵物': { en: 'No Pets', id: 'Tidak ada hewan peliharaan' }
        };

        // --- 初始化 ---
        window.initApp = async function() {
            document.getElementById('loading-overlay').style.opacity = '0';
            setTimeout(() => document.getElementById('loading-overlay').style.display = 'none', 500);
            if (window.isFirebaseMode && window.db) {
                const q = window.query(window.collection(window.db, "employers"));
                window.onSnapshot(q, (snapshot) => {
                    employers = [];
                    snapshot.forEach((doc) => employers.push({ ...doc.data(), docId: doc.id }));
                    employers.sort((a, b) => a.id.localeCompare(b.id));
                    refreshCurrentView();
                });
            } else {
                const data = localStorage.getItem(LOCAL_STORAGE_KEY);
                if (data) employers = JSON.parse(data);
                refreshCurrentView();
            }
            switchTab('english');
        };
        function refreshCurrentView() {
            if(!document.getElementById('admin-list-area').classList.contains('hidden')) renderAdminList();
            if(!document.getElementById('page-public').classList.contains('hidden')) renderPublicList();
        }

        // --- 寵物邏輯 (新) ---
        let currentPets = [];
        
        window.adjPet = function(v) {
            let el = document.getElementById('pet-add-qty');
            let n = parseInt(el.value) + v;
            if(n < 1) n = 1;
            el.value = n;
        }

        window.togglePetInputs = function() {
            const type = document.getElementById('pet-add-type').value;
            const qtyGroup = document.getElementById('pet-qty-group');
            const otherGroup = document.getElementById('pet-other-group');

            qtyGroup.classList.add('hidden');
            otherGroup.classList.add('hidden');

            if (type === '其他') {
                otherGroup.classList.remove('hidden');
                qtyGroup.classList.remove('hidden');
            } else if (type !== '沒有寵物' && type !== '') {
                qtyGroup.classList.remove('hidden');
            }
        }

        window.addPetToList = function() {
            const type = document.getElementById('pet-add-type').value;
            const qty = document.getElementById('pet-add-qty').value;
            
            if (!type) { alert('請選擇寵物類型'); return; }
            
            // 如果選沒有寵物，清空列表並只加一項
            if (type === '沒有寵物') {
                currentPets = [{text: '沒有寵物'}];
            } else {
                // 如果之前是沒有寵物，先移除
                if(currentPets.length === 1 && currentPets[0].text === '沒有寵物') currentPets = [];
                
                if (type === '其他') {
                    const otherText = document.getElementById('inp-pets-other-text').value;
                    if(!otherText) { alert('請以英文填寫寵物類型'); return; }
                    currentPets.push({text: `${qty}${otherText}`});
                    document.getElementById('inp-pets-other-text').value = '';
                } else {
                    const text = `${qty}${type}`;
                    currentPets.push({text: text});
                }
            }
            updatePetDisplay();
        }

        window.removePet = function(idx) {
            currentPets.splice(idx, 1);
            updatePetDisplay();
        }

        function updatePetDisplay() {
            const c = document.getElementById('pet-list-display');
            if (currentPets.length === 0) {
                c.innerHTML = '<span class="text-gray-400 italic self-center">尚未新增寵物</span>';
                document.getElementById('inp-pets-string').value = '';
            } else {
                c.innerHTML = currentPets.map((p, i) => `
                    <span class="bg-green-100 text-green-800 px-3 py-2 rounded text-base flex items-center">
                        ${p.text} <button type="button" onclick="removePet(${i})" class="ml-2 text-red-500 hover:text-red-700 font-bold">&times;</button>
                    </span>
                `).join('');
                document.getElementById('inp-pets-string').value = currentPets.map(p=>p.text).join(', ');
            }
        }

        // --- 薪金控制 ---
        window.adjustSalary = function(val) {
            let el = document.getElementById('inp-salary');
            let v = parseInt(el.value);
            if(isNaN(v)) v = 5100;
            v += val;
            if(v < 5100) v = 5100;
            el.value = v;
        }

        // --- 工人房邏輯 ---
        window.toggleRoomOther = function() {
            const v = document.getElementById('inp-room').value;
            if(v === '其他') document.getElementById('inp-room-other').classList.remove('hidden');
            else document.getElementById('inp-room-other').classList.add('hidden');
        }

        // --- 國籍邏輯 ---
        window.toggleNatOther = function() {
            const v = document.getElementById('inp-nationality').value;
            if(v === 'Other') document.getElementById('inp-nationality-other').classList.remove('hidden');
            else document.getElementById('inp-nationality-other').classList.add('hidden');
        }

        // --- 資料儲存 ---
        window.handleSave = async function() {
            const btn = document.getElementById('btn-save');
            btn.disabled = true;
            document.getElementById('btn-save-text').innerText = "處理中...";
            const isFishing = !document.getElementById('fishing-header').classList.contains('hidden');
            
            try {
                // 1. 收集資料
                const id = document.getElementById('inp-id').value;
                const loc = document.getElementById('inp-location').value;
                let nat = document.getElementById('inp-nationality').value;
                if (nat === 'Other') nat = document.getElementById('inp-nationality-other').value;

                const memStr = document.getElementById('inp-members-string').value;
                const petStr = document.getElementById('inp-pets-string').value;
                let room = document.getElementById('inp-room').value;
                if(room === '其他') room = document.getElementById('inp-room-other').value;
                
                const date = document.getElementById('inp-date').value;
                const salary = document.getElementById('inp-salary').value;
                const lang = document.getElementById('inp-lang').value;
                
                // 2. 驗證
                let errors = [];
                if (!id) errors.push("僱主編號");
                if (!loc) errors.push("地區");
                if (!nat) errors.push("國籍");
                if (!memStr) errors.push("家庭成員");
                if (!petStr) errors.push("寵物");
                if (!room) errors.push("工人房");
                if (!date) errors.push("上班日期");
                if (!salary) errors.push("薪金");
                if (!lang) errors.push("語言");

                if (errors.length > 0) throw new Error('你漏寫了問題，請檢查和填寫所有問題才可以上載成功');
                
                if (parseInt(salary) < 5100) {
                    alert('薪金錯誤，重新填寫');
                    throw new Error('薪金錯誤 (需 >= 5100)');
                }

                const data = {
                    id, location: loc, nationality: nat, members: memStr, pets: petStr, room, date, salary, lang,
                    remarks: document.getElementById('inp-remarks').value,
                    active: true,
                    updatedAt: new Date().toISOString()
                };
                
                const docId = document.getElementById('edit-doc-id').value;

                if (window.isFirebaseMode) {
                    if (docId) await window.updateDoc(window.doc(window.db, "employers", docId), data);
                    else await window.addDoc(window.collection(window.db, "employers"), data);
                } else {
                    if (docId) {
                        const idx = employers.findIndex(e => e.docId === docId);
                        if (idx !== -1) employers[idx] = { ...data, docId }; 
                    } else {
                        data.docId = String(Date.now());
                        employers.push(data);
                    }
                    employers.sort((a, b) => a.id.localeCompare(b.id));
                    localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(employers));
                    refreshCurrentView();
                }
                
                if (isFishing) {
                    alert('上載成功及已上架');
                    switchAdminSub('list');
                } else {
                    if (docId) alert('資料已更改及上架');
                    else alert('上載成功及已上架');
                    resetForm();
                }

            } catch (e) {
                if(!e.message.includes('薪金錯誤')) alert(e.message);
            } finally {
                btn.disabled = false;
                document.getElementById('btn-save-text').innerText = "上載資料";
            }
        };

        window.toggleActive = async function(docId, currentStatus) {
            if (window.isFirebaseMode) {
                await window.updateDoc(window.doc(window.db, "employers", docId), { active: !currentStatus });
            } else {
                const emp = employers.find(e => e.docId === docId);
                if(emp) {
                    emp.active = !currentStatus;
                    localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(employers));
                    refreshCurrentView();
                }
            }
        };

        window.deleteEmp = async function(docId) {
            if(!confirm('確認刪除？這將永久移除資料。')) return;
            if(!confirm('再次確認：您確定要永久刪除此資料嗎？')) return;
            if(window.isFirebaseMode) {
                await window.deleteDoc(window.doc(window.db, "employers", docId));
            } else {
                const idx = employers.findIndex(e => e.docId === docId);
                if(idx > -1) {
                    employers.splice(idx, 1);
                    localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(employers));
                    refreshCurrentView();
                }
            }
        };

        window.editEmp = function(docId) {
            const e = employers.find(emp => emp.docId === docId);
            if(!e) return;
            document.getElementById('inp-id').value = e.id;
            document.getElementById('inp-location').value = e.location;
            
            // Nat
            const stdNat = ['Filipino','Indonesian','Filipino & Indonesian','Indonesian & Filipino'];
            if(stdNat.includes(e.nationality)) {
                document.getElementById('inp-nationality').value = e.nationality;
                toggleNatOther();
            } else {
                document.getElementById('inp-nationality').value = 'Other';
                toggleNatOther();
                document.getElementById('inp-nationality-other').value = e.nationality;
            }

            document.getElementById('inp-room').value = e.room; 
            document.getElementById('inp-date').value = e.date;
            document.getElementById('inp-salary').value = e.salary;
            document.getElementById('inp-lang').value = e.lang;
            document.getElementById('inp-remarks').value = e.remarks || '';
            document.getElementById('edit-doc-id').value = docId;
            
            // Restore Members
            currentMembers = [];
            e.members.split(', ').forEach(p => currentMembers.push({text: p}));
            updateMemberDisplay();

            // Restore Pets
            currentPets = [];
            e.pets.split(', ').forEach(p => currentPets.push({text: p}));
            updatePetDisplay();

            switchAdminSub('input');
            window.scrollTo(0,0);
        };

        // --- 導航與管理 UI ---
        window.switchTab = function(tab) {
            document.getElementById('page-login').classList.add('hidden-section');
            document.getElementById('page-admin').classList.add('hidden-section');
            document.getElementById('page-public').classList.add('hidden-section');
            document.querySelectorAll('.btn-nav').forEach(b => b.classList.remove('active'));
            document.getElementById('nav-' + tab).classList.add('active');

            if (tab === 'admin') {
                if (sessionStorage.getItem('isAdminLoggedIn') === 'true') {
                    document.getElementById('page-admin').classList.remove('hidden-section');
                    switchAdminSub('input');
                } else {
                    document.getElementById('page-login').classList.remove('hidden-section');
                }
            } else {
                document.getElementById('page-public').classList.remove('hidden-section');
                currentLang = tab;
                currentPage = 1;
                renderPublicList();
            }
        };

        window.checkLogin = function() {
            if (document.getElementById('admin-password').value === PASSWORD) {
                sessionStorage.setItem('isAdminLoggedIn', 'true');
                document.getElementById('login-error').classList.add('hidden');
                document.getElementById('admin-password').value = '';
                switchTab('admin');
            } else {
                document.getElementById('login-error').classList.remove('hidden');
            }
        };

        window.switchAdminSub = function(sub) {
            document.getElementById('admin-input-area').classList.add('hidden');
            document.getElementById('admin-list-area').classList.add('hidden');
            document.getElementById('fishing-header').classList.add('hidden');

            if (sub === 'input') {
                document.getElementById('admin-input-area').classList.remove('hidden');
                if(document.getElementById('edit-doc-id').value === '') resetForm();
            } else if (sub === 'fishing') {
                document.getElementById('admin-input-area').classList.remove('hidden');
                document.getElementById('fishing-header').classList.remove('hidden');
                generateFishingData();
            } else if (sub === 'list') {
                document.getElementById('admin-list-area').classList.remove('hidden');
                renderAdminList();
            }
        };

        function renderAdminList() {
            const c = document.getElementById('admin-records-container');
            c.innerHTML = '';
            document.getElementById('db-status-text').innerText = window.isFirebaseMode ? "(Firebase Cloud)" : "(Offline Local Storage)";
            if(employers.length === 0) { c.innerHTML = '<p class="text-center text-gray-400 text-lg">暫無資料</p>'; return; }

            employers.forEach((e) => {
                const status = e.active ? '<span class="text-green-600 font-bold">[上架中]</span>' : '<span class="text-red-600 font-bold">[已下架]</span>';
                const html = `
                    <div class="bg-white p-6 rounded-lg shadow border border-l-8 ${e.active ? 'border-l-green-500' : 'border-l-red-500'}">
                        <div class="flex flex-col sm:flex-row justify-between items-start mb-4 border-b pb-4">
                            <h4 class="font-bold text-2xl text-blue-800">${e.id} ${status}</h4>
                            <div class="space-x-4 mt-2 sm:mt-0">
                                <button onclick="editEmp('${e.docId}')" class="bg-blue-100 text-blue-700 px-4 py-2 rounded font-bold hover:bg-blue-200">修改</button>
                                <button onclick="toggleActive('${e.docId}', ${e.active})" class="bg-yellow-100 text-yellow-700 px-4 py-2 rounded font-bold hover:bg-yellow-200">${e.active ? '下架' : '上架'}</button>
                                <button onclick="deleteEmp('${e.docId}')" class="bg-red-100 text-red-700 px-4 py-2 rounded font-bold hover:bg-red-200">刪除</button>
                            </div>
                        </div>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-3 text-lg text-gray-700">
                            <p><span class="font-bold text-gray-500">地區:</span> ${e.location}</p>
                            <p><span class="font-bold text-gray-500">國籍:</span> ${e.nationality || '-'}</p>
                            <p><span class="font-bold text-gray-500">成員:</span> ${e.members}</p>
                            <p><span class="font-bold text-gray-500">寵物:</span> ${e.pets}</p>
                            <p><span class="font-bold text-gray-500">房間:</span> ${e.room}</p>
                            <p><span class="font-bold text-gray-500">日期:</span> ${e.date}</p>
                            <p><span class="font-bold text-gray-500">薪金:</span> $${e.salary}</p>
                            <p><span class="font-bold text-gray-500">語言:</span> ${e.lang}</p>
                            <p class="col-span-full"><span class="font-bold text-gray-500">備註:</span> ${e.remarks || '-'}</p>
                        </div>
                    </div>
                `;
                c.innerHTML += html;
            });
        }

        // --- 成員輸入 ---
        let currentMembers = [];
        window.toggleAgeInput = function() {
            const type = document.getElementById('mem-type').value;
            const group = document.getElementById('age-input-group');
            if (type === '成人') group.classList.add('hidden'); // Only Adult hides
            else group.classList.remove('hidden'); // Others show
        };
        window.addMember = function() {
            const type = document.getElementById('mem-type').value;
            const age = document.getElementById('mem-age').value;
            
            if (type !== '成人' && !age) { alert('請輸入年齡'); return; }
            const text = (type === '成人') ? "1成人" : `1${type}(${age}歲)`;
            currentMembers.push({text});
            updateMemberDisplay();
            document.getElementById('mem-age').value = '';
        };
        window.removeMember = function(i) {
            currentMembers.splice(i, 1);
            updateMemberDisplay();
        };
        function updateMemberDisplay() {
            const c = document.getElementById('member-list-display');
            
            // Sort order logic
            const sortOrder = ['成人','小孩','長者','男孩','女孩','婆婆','公公'];
            // Mapping for display purposes is tricky with raw strings like "1小孩(5歲)"
            // But user just asked for sorting the OPTIONS in select, I did that.
            // He also asked "所有選項排序是...", which applies to select options.
            // I will just render them in insertion order or grouped. Grouping is safer.
            // Let's just render as added for now to allow deletion correctly by index.
            
            c.innerHTML = currentMembers.map((m,i)=>`<span class="bg-blue-100 text-blue-800 px-3 py-1 rounded text-base flex items-center">${m.text} <button type="button" onclick="removeMember(${i})" class="ml-2 text-red-500 font-bold">&times;</button></span>`).join('');
            
            // Consolidate for string
            // Logic: 2成人, 1小孩(5歲), ...
            let adult=0, others=[];
            currentMembers.forEach(m=>{ if(m.text.includes('成人')) adult++; else others.push(m.text); });
            let s=[]; if(adult>0)s.push(`${adult}成人`); s=s.concat(others);
            document.getElementById('inp-members-string').value = s.join(', ');
        }

        // --- 釣魚盤邏輯 ---
        window.generateFishingData = function() {
            let maxU = 1000;
            employers.forEach(e => { if(e.id.startsWith('U')) { let n=parseInt(e.id.substring(1)); if(n>maxU)maxU=n; }});
            document.getElementById('inp-id').value = 'U' + (maxU + 1);
            const st = Object.keys(locationMap);
            document.getElementById('inp-location').value = st[Math.floor(Math.random()*st.length)];
            const nats = ['Filipino', 'Indonesian', 'Filipino & Indonesian', 'Indonesian & Filipino']; // Exclude Other
            document.getElementById('inp-nationality').value = nats[Math.floor(Math.random()*nats.length)];
            toggleNatOther();

            // Family: Max 2 Adults, 2 Kids, 2 Elders
            currentMembers = [];
            const a = Math.floor(Math.random()*2)+1; // 1-2 Adults
            for(let i=0; i<a; i++) currentMembers.push({text: "1成人"});
            
            if(Math.random()>0.5) {
                const k = Math.floor(Math.random()*2)+1; // 1-2 Kids
                for(let i=0; i<k; i++) currentMembers.push({text: `1小孩(${Math.floor(Math.random()*17)}歲)`});
            }
            if(Math.random()>0.7) {
                const e = Math.floor(Math.random()*2)+1; // 1-2 Elders
                for(let i=0; i<e; i++) currentMembers.push({text: `1長者(${Math.floor(Math.random()*31)+60}歲)`});
            }
            updateMemberDisplay();

            // Pets: No, Small, Medium, Big, Cat. Qty 1-2
            currentPets = [];
            const pTypes = ['沒有寵物', '小狗', '中狗', '大狗', '貓'];
            const p = pTypes[Math.floor(Math.random()*pTypes.length)];
            if(p === '沒有寵物') {
                currentPets.push({text: '沒有寵物'});
            } else {
                const qty = Math.floor(Math.random()*2)+1;
                currentPets.push({text: `${qty}${p}`});
            }
            updatePetDisplay();

            // Room: Probabilities
            const rOpts = ['有工人房','有工人房','有工人房','與小孩同房','與長者同房','與另外傭同房','與女僱主同房'];
            document.getElementById('inp-room').value = rOpts[Math.floor(Math.random()*rOpts.length)];
            toggleRoomOther();

            // Salary: 5500 most
            const sals = [5100, 5300, 5500, 5500, 5500, 5800, 6000];
            document.getElementById('inp-salary').value = sals[Math.floor(Math.random()*sals.length)];

            // Lang: Eng most
            const langs = ['英文','英文','英文','廣東話','普通話','英文&廣東話','英文&普通話'];
            document.getElementById('inp-lang').value = langs[Math.floor(Math.random()*langs.length)];

            document.getElementById('inp-date').value = '盡快';
            document.getElementById('inp-remarks').value = '';
        };

        window.resetForm = function() {
            document.getElementById('employer-form').reset();
            document.getElementById('edit-doc-id').value = '';
            currentMembers = []; updateMemberDisplay();
            currentPets = []; updatePetDisplay();
            toggleNatOther();
            toggleRoomOther();
            togglePetInputs();
        };

        // --- 公開列表 ---
        function renderPublicList() {
            const container = document.getElementById('public-records-container');
            container.innerHTML = '';
            const activeEmps = employers.filter(e => e.active);
            const totalPages = Math.ceil(activeEmps.length / itemsPerPage);
            const start = (currentPage - 1) * itemsPerPage;
            const pageData = activeEmps.slice(start, start + itemsPerPage);
            const isEn = currentLang === 'english';
            
            // Banner Text & Styling
            const bannerBox = document.getElementById('banner-box');
            const bannerText = document.getElementById('banner-text');
            if(isEn) {
                bannerBox.className = "w-full max-w-3xl mb-6 p-6 rounded-xl text-center shadow-md border-l-8 border-green-500 bg-green-50";
                bannerText.innerText = "💵All helpers NO placement fee, NO agency fee, NO registration fee.\nApply job or refer your friends, click the button below to WhatsApp us.";
                document.getElementById('public-title').innerText = 'Employer List';
                document.getElementById('nav-indonesian').innerText = '🇮🇩 Indonesian';
                document.getElementById('btn-prev').innerText = 'Prev';
                document.getElementById('btn-next').innerText = 'Next';
            } else {
                bannerBox.className = "w-full max-w-3xl mb-6 p-6 rounded-xl text-center shadow-md border-l-8 border-red-500 bg-red-50";
                bannerText.innerText = "💵Semua pekerja tanpa biaya penempatan, tanpa biaya agen, dan tanpa biaya pendaftaran.\nIngin melamar kerja atau merekomendasikan teman? Klik tombol di bawah untuk WhatsApp kami.";
                document.getElementById('public-title').innerText = 'Daftar Majikan';
                document.getElementById('nav-indonesian').innerText = '🇮🇩 Indonesian';
                document.getElementById('btn-prev').innerText = 'Sebelumnya';
                document.getElementById('btn-next').innerText = 'Selanjutnya';
            }

            if(pageData.length === 0) {
                container.innerHTML = '<div class="col-span-full text-center p-10 text-gray-500 text-xl">No Data / Tidak Ada Data</div>';
                updatePageIndicator(0, 0, isEn);
                return;
            }

            pageData.forEach(e => {
                const tLoc = translateText(e.location, currentLang);
                const tNat = e.nationality; // Keep English
                const tMem = translateText(e.members, currentLang); // Remove (s) in helper
                const tPet = translatePets(e.pets, currentLang);
                const tRoom = getDict(e.room, currentLang);
                const tDate = getDict(e.date, currentLang);
                const tLang = getDict(e.lang, currentLang);
                let tRem = e.remarks;
                if (!isEn && e.remarks) tRem = e.remarks.replace(/好僱主/g, 'Majikan Baik').replace(/準時出糧/g, 'Gaji Tepat Waktu');

                // Formatting
                const idD = isEn ? `Employer Number: ${e.id}` : `Nomor Pemberi Kerja: ${e.id}`;
                const salD = isEn ? `Salary $${e.salary}` : `Gaji $${e.salary}`;

                // Red text logic
                const petClass = (tPet.includes('No Pets') || tPet.includes('Tidak ada hewan')) ? "text-red-600 font-bold" : "text-gray-800 font-medium";
                const roomClass = (tRoom.includes('have own room') || tRoom.includes('ada kamar sendiri')) ? "text-red-600 font-bold" : "text-gray-800 font-medium";
                const dateClass = (tDate === 'ASAP' || tDate === 'Secepatnya') ? "text-red-600 font-bold" : "text-gray-800 font-medium";

                const L = {
                    loc: isEn ? 'Employer Location' : 'Lokasi Majikan',
                    nat: isEn ? 'Nationality' : 'Kewarganegaraan',
                    fam: isEn ? 'Employer Member & Age' : 'Anggota Keluarga & Usia',
                    pets: isEn ? 'Pets' : 'Hewan Peliharaan',
                    room: isEn ? 'Helper Room' : 'Kamar Pembantu',
                    date: isEn ? 'START of WORK DAY' : 'Awal Hari Kerja',
                    lang: isEn ? 'Language' : 'Bahasa',
                    rem: isEn ? 'Remarks' : 'Catatan'
                };

                const html = `
                    <div class="bg-white rounded-xl shadow-xl border-t-8 border-blue-600 overflow-hidden flex flex-col p-6 space-y-4">
                        <div class="flex flex-col border-b pb-3 mb-2">
                            <h3 class="text-2xl font-bold text-gray-900">${idD}</h3>
                            <span class="text-2xl font-bold text-green-600 mt-1">${salD}</span>
                        </div>
                        <div><p class="text-sm text-gray-400 font-bold uppercase tracking-wide">${L.loc}</p><p class="font-medium text-gray-800 text-xl">${tLoc}</p></div>
                        <div><p class="text-sm text-gray-400 font-bold uppercase tracking-wide">${L.nat}</p><p class="font-medium text-gray-800 text-xl">${tNat}</p></div>
                        <div><p class="text-sm text-gray-400 font-bold uppercase tracking-wide">${L.fam}</p><p class="font-medium text-gray-800 text-xl">${tMem}</p></div>
                        <div><p class="text-sm text-gray-400 font-bold uppercase tracking-wide">${L.pets}</p><p class="${petClass} text-xl">${tPet}</p></div>
                        <div><p class="text-sm text-gray-400 font-bold uppercase tracking-wide">${L.room}</p><p class="${roomClass} text-xl">${tRoom}</p></div>
                        <div><p class="text-sm text-gray-400 font-bold uppercase tracking-wide">${L.date}</p><p class="${dateClass} text-xl">${tDate}</p></div>
                        <div><p class="text-sm text-gray-400 font-bold uppercase tracking-wide">${L.lang}</p><p class="font-medium text-blue-800 text-xl">${tLang}</p></div>
                        ${e.remarks ? `<div class="bg-yellow-50 p-3 rounded border border-yellow-100"><p class="text-sm text-gray-500 font-bold uppercase">${L.rem}</p><p class="text-lg text-gray-700 italic">${tRem}</p></div>` : ''}
                    </div>
                `;
                container.innerHTML += html;
            });
            updatePageIndicator(currentPage, totalPages, isEn);
        }

        function updatePageIndicator(curr, total, isEn) {
            const label = isEn ? 'Page' : 'Halaman';
            document.getElementById('page-indicator').innerText = `${label} ${curr} / ${total || 1}`;
        }

        // --- Translation Helpers ---
        function translateText(text, lang) {
            if(!text) return '-';
            let res = text;
            for(let cn in locationMap) { if(res.includes(cn)) res = res.replace(cn, locationMap[cn]); }
            
            // Remove plural (s) for English
            if(lang === 'english') {
                res = res.replace(/成人/g, ' Adult')
                         .replace(/小孩/g, ' Child')
                         .replace(/長者/g, ' Elderly')
                         .replace(/男孩/g, ' Boy')
                         .replace(/女孩/g, ' Girl')
                         .replace(/婆婆/g, ' Grandma')
                         .replace(/公公/g, ' Grandpa')
                         .replace(/歲/g, 'yo');
            } else {
                res = res.replace(/成人/g, ' Dewasa')
                         .replace(/小孩/g, ' Anak')
                         .replace(/長者/g, ' Lansia')
                         .replace(/男孩/g, ' Anak Laki-laki')
                         .replace(/女孩/g, ' Anak Perempuan')
                         .replace(/婆婆/g, ' Nenek')
                         .replace(/公公/g, ' Kakek')
                         .replace(/歲/g, 'th');
            }
            return res;
        }
        
        function translatePets(text, lang) {
            if(!text) return '-';
            if(text === '沒有寵物') return getDict('沒有寵物', lang);
            
            // Split by comma for multiple pets
            const parts = text.split(', ');
            const translatedParts = parts.map(p => {
                const match = p.match(/^(\d+)(.+)$/);
                if(match) {
                    const qty = match[1];
                    const name = match[2];
                    const tName = getDict(name, lang); 
                    return `${qty} ${tName}`;
                }
                return p; 
            });
            return translatedParts.join(', ');
        }

        function getDict(key, lang) {
            const t = lang === 'english' ? 'en' : 'id';
            if(dict[key] && dict[key][t]) return dict[key][t];
            // Fallback check for "Other" (custom text)
            if(key && key.length > 0) return key; 
            return '-';
        }
        
        window.changePage = function(dir) {
            const activeEmps = employers.filter(e => e.active);
            const totalPages = Math.ceil(activeEmps.length / itemsPerPage);
            let newPage = currentPage + dir;
            if (newPage < 1) newPage = 1; else if (newPage > totalPages) newPage = totalPages;
            if (newPage !== currentPage) { currentPage = newPage; renderPublicList(); window.scrollTo(0, 0); }
        };
    </script>
</body>
</html>
