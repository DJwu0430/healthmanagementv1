<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>個人健康管理系統</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Noto Sans TC', sans-serif;
            background-color: #f0f9ff;
            color: #1e3a8a;
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .tab { transition: all 0.3s ease; }
        .tab.active { background-color: #3b82f6; color: white; }
        .progress-bar { transition: width 0.5s ease-in-out; }
        .record-item { animation: fadeIn 0.5s; }
        .bottle-btn { animation: scaleIn 0.3s; }
        .water-drop { animation: dropIn 0.5s; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes scaleIn { from { transform: scale(0.9); opacity: 0.5; } to { transform: scale(1); opacity: 1; } }
        @keyframes dropIn { 0% { transform: translateY(-20px); opacity: 0; } 70% { transform: translateY(5px); } 100% { transform: translateY(0); opacity: 1; } }
        .bottle-colors { background: linear-gradient(135deg, var(--start-color), var(--end-color)); }
    </style>
</head>
<body class="min-h-screen pb-20">
    <div class="max-w-md mx-auto bg-white shadow-lg rounded-lg overflow-hidden">
        <!-- 頂部日期顯示 -->
        <div class="bg-gradient-to-r from-blue-500 to-indigo-600 p-4 text-white">
            <div class="flex justify-between items-center">
                <h1 class="text-xl font-bold">健康管理</h1>
                <div id="currentDate" class="text-sm font-medium"></div>
            </div>
        </div>
        <!-- 分頁導航 -->
        <div class="flex overflow-x-auto bg-gray-100 border-b border-gray-200">
            <button class="tab flex-1 py-3 px-2 text-center text-sm font-medium" data-tab="diet">飲食</button>
            <button class="tab active flex-1 py-3 px-2 text-center text-sm font-medium" data-tab="water">飲水</button>
            <button class="tab flex-1 py-3 px-2 text-center text-sm font-medium" data-tab="exercise">運動</button>
            <button class="tab flex-1 py-3 px-2 text-center text-sm font-medium" data-tab="physical">生理</button>
            <button class="tab flex-1 py-3 px-2 text-center text-sm font-medium" data-tab="sleep">睡眠</button>
        </div>
        <!-- 飲食分頁 -->
        <div id="dietTab" class="tab-content p-4">
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">今日營養攝取</h2>
                <div class="mb-4">
                    <div class="flex justify-between mb-1">
                        <span>蛋白質 (<span id="proteinCount">0</span>/6份)</span>
                        <span id="proteinPercent">0%</span>
                    </div>
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div id="proteinBar" class="progress-bar bg-blue-600 h-2.5 rounded-full" style="width: 0%"></div>
                    </div>
                </div>
                <div class="mb-4">
                    <div class="flex justify-between mb-1">
                        <span>蔬菜 (<span id="veggieCount">0</span>/3份)</span>
                        <span id="veggiePercent">0%</span>
                    </div>
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div id="veggieBar" class="progress-bar bg-green-500 h-2.5 rounded-full" style="width: 0%"></div>
                    </div>
                </div>
                <div class="mb-4">
                    <div class="flex justify-between mb-1">
                        <span>水果 (<span id="fruitCount">0</span>/2份)</span>
                        <span id="fruitPercent">0%</span>
                    </div>
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div id="fruitBar" class="progress-bar bg-yellow-500 h-2.5 rounded-full" style="width: 0%"></div>
                    </div>
                </div>
                <div class="mb-4">
                    <div class="flex justify-between mb-1">
                        <span>澱粉 (<span id="starchCount">0</span>/2份)</span>
                        <span id="starchPercent">0%</span>
                    </div>
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div id="starchBar" class="progress-bar bg-orange-500 h-2.5 rounded-full" style="width: 0%"></div>
                    </div>
                </div>
            </div>
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">新增飲食紀錄</h2>
                <div class="grid grid-cols-4 gap-2 mb-4">
                    <div class="flex flex-row items-center justify-center space-x-1">
                        <button id="addProtein" class="bg-blue-100 hover:bg-blue-200 text-blue-800 font-bold py-2 px-3 rounded-lg transition text-md flex items-center">🥩+1</button>
                        <button id="subProtein" class="bg-blue-50 hover:bg-blue-100 text-blue-800 font-medium p-1 rounded-lg border border-blue-300 transition text-xs flex items-center">-1</button>
                    </div>
                    <div class="flex flex-row items-center justify-center space-x-1">
                        <button id="addVeggie" class="bg-green-100 hover:bg-green-200 text-green-800 font-bold py-2 px-3 rounded-lg transition text-md flex items-center">🥦+1</button>
                        <button id="subVeggie" class="bg-green-50 hover:bg-green-100 text-green-800 font-medium p-1 rounded-lg border border-green-300 transition text-xs flex items-center">-1</button>
                    </div>
                    <div class="flex flex-row items-center justify-center space-x-1">
                        <button id="addFruit" class="bg-yellow-100 hover:bg-yellow-200 text-yellow-800 font-bold py-2 px-3 rounded-lg transition text-md flex items-center">🍎+1</button>
                        <button id="subFruit" class="bg-yellow-50 hover:bg-yellow-100 text-yellow-800 font-medium p-1 rounded-lg border border-yellow-300 transition text-xs flex items-center">-1</button>
                    </div>
                    <div class="flex flex-row items-center justify-center space-x-1">
                        <button id="addStarch" class="bg-orange-100 hover:bg-orange-200 text-orange-800 font-bold py-2 px-3 rounded-lg transition text-md flex items-center">🍚+1</button>
                        <button id="subStarch" class="bg-orange-50 hover:bg-orange-100 text-orange-800 font-medium p-1 rounded-lg border border-orange-300 transition text-xs flex items-center">-1</button>
                    </div>
                </div>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">餐點名稱</label>
                    <input type="text" id="mealName" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="例：早餐三明治">
                </div>
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">用餐時間</label>
                        <input type="time" id="mealTime" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">花費 (NT$)</label>
                        <input type="number" id="mealCost" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="選填">
                    </div>
                </div>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">熱量 (kcal)</label>
                    <input type="number" id="mealCalories" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="選填">
                </div>
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">蛋白質 (份)</label>
                        <input type="number" id="mealProtein" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" value="0" min="0">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">蔬菜 (份)</label>
                        <input type="number" id="mealVeggie" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" value="0" min="0">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">水果 (份)</label>
                        <input type="number" id="mealFruit" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" value="0" min="0">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">澱粉 (份)</label>
                        <input type="number" id="mealStarch" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" value="0" min="0">
                    </div>
                </div>
                <button id="saveMeal" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition">儲存飲食紀錄</button>
            </div>
            <div class="mt-6">
                <button id="viewDietRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition flex items-center justify-center">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                    </svg>
                    查看飲食紀錄
                </button>
            </div>
        </div>
        <!-- 飲水分頁 -->
        <div id="waterTab" class="tab-content active p-4">
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">今日飲水量</h2>
                <div class="mb-4">
                    <div class="flex justify-between mb-1">
                        <span>飲水量 (<span id="waterIntake">0</span>/2000cc)</span>
                        <span id="waterPercent">0%</span>
                    </div>
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div id="waterBar" class="progress-bar bg-cyan-500 h-2.5 rounded-full" style="width: 0%"></div>
                    </div>
                </div>
                <div class="flex items-center justify-center my-4">
                    <div class="water-drop text-5xl">💧</div>
                </div>
            </div>
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">我的水壺</h2>
                <div id="bottleContainer" class="grid grid-cols-2 gap-3 mb-4"></div>
                <button id="addBottleBtn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition mt-2">新增水壺</button>
            </div>
            <div class="mt-6">
                <button id="viewWaterRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition flex items-center justify-center">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                    </svg>
                    查看飲水紀錄
                </button>
            </div>
        </div>
        <!-- 運動分頁 -->
        <div id="exerciseTab" class="tab-content p-4">
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">新增運動紀錄</h2>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">運動類型</label>
                    <select id="exerciseType" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option value="跑步">跑步</option>
                        <option value="健走">健走</option>
                        <option value="游泳">游泳</option>
                        <option value="騎車">騎車</option>
                        <option value="重訓">重訓</option>
                        <option value="其他">其他</option>
                    </select>
                </div>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">運動時間</label>
                    <input type="datetime-local" id="exerciseTime" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">時長 (分鐘)</label>
                    <input type="number" id="exerciseDuration" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="1">
                </div>
                <div id="distanceGroup" class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">距離 (公里)</label>
                    <input type="number" id="exerciseDistance" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0" step="0.01">
                </div>
                <div id="strengthGroup" class="mb-4 hidden">
                    <label class="block text-sm font-medium text-gray-700 mb-1">訓練部位</label>
                    <div class="grid grid-cols-2 gap-2 mb-2">
                        <label><input type="checkbox" value="胸" class="mr-1 strength-part">胸</label>
                        <label><input type="checkbox" value="背" class="mr-1 strength-part">背</label>
                        <label><input type="checkbox" value="腹部核心" class="mr-1 strength-part">腹部核心</label>
                        <label><input type="checkbox" value="背部核心" class="mr-1 strength-part">背部核心</label>
                        <label><input type="checkbox" value="腿" class="mr-1 strength-part">腿</label>
                        <label><input type="checkbox" value="手臂" class="mr-1 strength-part">手臂</label>
                        <label><input type="checkbox" value="肩膀" class="mr-1 strength-part">肩膀</label>
                    </div>
                    <div id="strengthSetsContainer"></div>
                    <button type="button" id="addStrengthSet" class="mt-2 bg-blue-100 hover:bg-blue-200 text-blue-800 font-medium py-1 px-3 rounded-lg transition">新增訓練組</button>
                </div>
                <div id="otherGroup" class="mb-4 hidden">
                    <label class="block text-sm font-medium text-gray-700 mb-1">備註</label>
                    <input type="text" id="exerciseNote" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <button id="saveExercise" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition">儲存運動紀錄</button>
            </div>
            <div class="mt-6">
                <button id="viewExerciseRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition flex items-center justify-center">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                    </svg>
                    查看運動紀錄
                </button>
            </div>
        </div>
        <!-- 生理分頁 -->
        <div id="physicalTab" class="tab-content p-4">
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">身高體重紀錄</h2>
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">身高 (cm)</label>
                        <input type="number" id="heightInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">體重 (kg)</label>
                        <input type="number" id="weightInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0">
                    </div>
                </div>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">BMI</label>
                    <input type="text" id="bmiInput" class="w-full px-3 py-2 border border-gray-300 rounded-md bg-gray-100" readonly>
                </div>
                <button id="saveHeightWeight" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition mb-2">儲存紀錄</button>
                <button id="viewHeightWeightRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition">查看紀錄</button>
            </div>
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">體脂/內臟脂肪/肌肉率</h2>
                <div class="grid grid-cols-3 gap-4 mb-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">體脂率 (%)</label>
                        <input type="number" id="fatInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0" max="100">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">內臟脂肪</label>
                        <input type="number" id="visFatInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">肌肉率 (%)</label>
                        <input type="number" id="muscleInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0" max="100">
                    </div>
                </div>
                <button id="saveBodyComp" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition mb-2">儲存紀錄</button>
                <button id="viewBodyCompRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition">查看紀錄</button>
            </div>
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">血壓紀錄 <span class="text-xs text-gray-500">(標準: 收縮壓110-130、舒張壓70-90)</span></h2>
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">收縮壓</label>
                        <input type="number" id="sysBPInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">舒張壓</label>
                        <input type="number" id="diaBPInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0">
                    </div>
                </div>
                <button id="saveBP" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition mb-2">儲存紀錄</button>
                <button id="viewBPRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition">查看紀錄</button>
            </div>
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">血糖紀錄 <span class="text-xs text-gray-500">(標準: 飯前/空腹80-100、飯後100-150)</span></h2>
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">空腹/飯前</label>
                        <input type="number" id="sugarFastingInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">飯後(30分鐘)</label>
                        <input type="number" id="sugarAfterInput" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" min="0">
                    </div>
                </div>
                <button id="saveSugar" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition mb-2">儲存紀錄</button>
                <button id="viewSugarRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition">查看紀錄</button>
            </div>
        </div>
        <!-- 睡眠分頁 -->
        <div id="sleepTab" class="tab-content p-4">
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-3">新增睡眠紀錄</h2>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">日期</label>
                    <input type="text" id="sleepDate" class="w-full px-3 py-2 border border-gray-300 rounded-md bg-gray-100" readonly>
                </div>
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">就寢時間</label>
                        <input type="time" id="sleepStart" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">起床時間</label>
                        <input type="time" id="sleepEnd" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                    </div>
                </div>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">睡眠品質 (1~5分)</label>
                    <select id="sleepQuality" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option value="1">1</option>
                        <option value="2">2</option>
                        <option value="3">3</option>
                        <option value="4">4</option>
                        <option value="5">5</option>
                    </select>
                </div>
                <button id="saveSleep" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition">儲存睡眠紀錄</button>
            </div>
            <div class="mt-6">
                <button id="viewSleepRecords" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium py-2 px-4 rounded-md transition flex items-center justify-center">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                    </svg>
                    查看睡眠紀錄
                </button>
            </div>
        </div>
    </div>
    <!-- 紀錄查看模態框 -->
    <div id="recordsModal" class="fixed inset-0 bg-black bg-opacity-50 hidden z-50 flex items-center justify-center">
        <div class="bg-white rounded-lg max-w-md w-full max-h-[80vh] overflow-hidden">
            <div class="p-4 border-b border-gray-200 flex justify-between items-center">
                <h3 id="modalTitle" class="text-lg font-semibold">紀錄查看</h3>
                <button id="closeModal" class="text-gray-500 hover:text-gray-700">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                    </svg>
                </button>
            </div>
            <div id="modalContent" class="p-4 overflow-y-auto max-h-[calc(80vh-60px)]">
                <!-- 紀錄內容會在這裡動態生成 -->
            </div>
        </div>
    </div>
    <!-- 新增水壺模態框 -->
    <div id="addBottleModal" class="fixed inset-0 bg-black bg-opacity-50 hidden z-50 flex items-center justify-center">
        <div class="bg-white rounded-lg max-w-md w-full overflow-hidden">
            <div class="p-4 border-b border-gray-200 flex justify-between items-center">
                <h3 class="text-lg font-semibold">新增水壺</h3>
                <button id="closeBottleModal" class="text-gray-500 hover:text-gray-700">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                    </svg>
                </button>
            </div>
            <div class="p-4">
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">水壺名稱</label>
                    <input type="text" id="bottleName" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="例：小杯">
                </div>
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">容量 (cc)</label>
                    <input type="number" id="bottleCapacity" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="例：300">
                </div>
                <button id="saveBottle" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-md transition">儲存</button>
            </div>
        </div>
    </div>
    <script>
    document.addEventListener('DOMContentLoaded', function() {
        // 設置當前日期
        const now = new Date();
        const options = { year: 'numeric', month: 'long', day: 'numeric', weekday: 'long' };
        document.getElementById('currentDate').textContent = now.toLocaleDateString('zh-TW', options);

        // 分頁切換初始化
        const tabs = document.querySelectorAll('.tab');
        const tabContents = document.querySelectorAll('.tab-content');
        tabs.forEach(tab => {
            tab.addEventListener('click', () => {
                const tabId = tab.getAttribute('data-tab');
                tabs.forEach(t => t.classList.remove('active'));
                tabContents.forEach(c => c.classList.remove('active'));
                tab.classList.add('active');
                document.getElementById(`${tabId}Tab`).classList.add('active');
            });
        });

        // showModal/closeModal
        function showModal(title, content) {
            document.getElementById('modalTitle').textContent = title;
            document.getElementById('modalContent').innerHTML = content;
            document.getElementById('recordsModal').classList.remove('hidden');
        }
        document.getElementById('closeModal').addEventListener('click', () => {
            document.getElementById('recordsModal').classList.add('hidden');
        });

        // ===== 五大分頁 JS 功能區 =====
        // --- 飲食與飲水分頁功能 ---
        if (!localStorage.getItem('dietRecords')) {
            localStorage.setItem('dietRecords', JSON.stringify([]));
        }
        if (!localStorage.getItem('dailyNutrition')) {
            localStorage.setItem('dailyNutrition', JSON.stringify({
                date: getCurrentDateString(),
                protein: 0,
                veggie: 0,
                fruit: 0,
                starch: 0
            }));
        }
        if (!localStorage.getItem('waterBottles')) {
            localStorage.setItem('waterBottles', JSON.stringify([]));
        }
        if (!localStorage.getItem('dailyWater')) {
            localStorage.setItem('dailyWater', JSON.stringify({
                date: getCurrentDateString(),
                intake: 0
            }));
        }
        if (!localStorage.getItem('waterRecords')) {
            localStorage.setItem('waterRecords', JSON.stringify([]));
        }
        function getCurrentDateString() {
            const now = new Date();
            return now.toISOString().split('T')[0];
        }
        function checkAndUpdateDailyData() {
            const today = getCurrentDateString();
            // 檢查營養攝取
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            if (dailyNutrition.date !== today) {
                dailyNutrition.date = today;
                dailyNutrition.protein = 0;
                dailyNutrition.veggie = 0;
                dailyNutrition.fruit = 0;
                dailyNutrition.starch = 0;
                localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            }
            // 檢查飲水量
            const dailyWater = JSON.parse(localStorage.getItem('dailyWater'));
            if (dailyWater.date !== today) {
                dailyWater.date = today;
                dailyWater.intake = 0;
                localStorage.setItem('dailyWater', JSON.stringify(dailyWater));
            }
            updateNutritionDisplay();
            updateWaterDisplay();
            renderBottles();
        }
        // --- 飲食分頁 ---
        const addProteinBtnRaw = document.getElementById('addProtein');
        addProteinBtnRaw.replaceWith(addProteinBtnRaw.cloneNode(true));
        const addProteinBtn = document.getElementById('addProtein');
        addProteinBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.protein += 1;
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const subProteinBtnRaw = document.getElementById('subProtein');
        subProteinBtnRaw.replaceWith(subProteinBtnRaw.cloneNode(true));
        const subProteinBtn = document.getElementById('subProtein');
        subProteinBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.protein = Math.max(0, dailyNutrition.protein - 1);
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const addVeggieBtnRaw = document.getElementById('addVeggie');
        addVeggieBtnRaw.replaceWith(addVeggieBtnRaw.cloneNode(true));
        const addVeggieBtn = document.getElementById('addVeggie');
        addVeggieBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.veggie += 1;
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const subVeggieBtnRaw = document.getElementById('subVeggie');
        subVeggieBtnRaw.replaceWith(subVeggieBtnRaw.cloneNode(true));
        const subVeggieBtn = document.getElementById('subVeggie');
        subVeggieBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.veggie = Math.max(0, dailyNutrition.veggie - 1);
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const addFruitBtnRaw = document.getElementById('addFruit');
        addFruitBtnRaw.replaceWith(addFruitBtnRaw.cloneNode(true));
        const addFruitBtn = document.getElementById('addFruit');
        addFruitBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.fruit += 1;
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const subFruitBtnRaw = document.getElementById('subFruit');
        subFruitBtnRaw.replaceWith(subFruitBtnRaw.cloneNode(true));
        const subFruitBtn = document.getElementById('subFruit');
        subFruitBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.fruit = Math.max(0, dailyNutrition.fruit - 1);
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const addStarchBtnRaw = document.getElementById('addStarch');
        addStarchBtnRaw.replaceWith(addStarchBtnRaw.cloneNode(true));
        const addStarchBtn = document.getElementById('addStarch');
        addStarchBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.starch += 1;
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const subStarchBtnRaw = document.getElementById('subStarch');
        subStarchBtnRaw.replaceWith(subStarchBtnRaw.cloneNode(true));
        const subStarchBtn = document.getElementById('subStarch');
        subStarchBtn.addEventListener('click', () => {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.starch = Math.max(0, dailyNutrition.starch - 1);
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
        });
        const saveMealBtn = document.getElementById('saveMeal');
        const viewDietRecordsBtn = document.getElementById('viewDietRecords');
        function updateNutritionDisplay() {
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            document.getElementById('proteinCount').textContent = dailyNutrition.protein;
            document.getElementById('veggieCount').textContent = dailyNutrition.veggie;
            document.getElementById('fruitCount').textContent = dailyNutrition.fruit;
            document.getElementById('starchCount').textContent = dailyNutrition.starch;
            const proteinPercent = Math.min(dailyNutrition.protein / 6 * 100, 100);
            const veggiePercent = Math.min(dailyNutrition.veggie / 3 * 100, 100);
            const fruitPercent = Math.min(dailyNutrition.fruit / 2 * 100, 100);
            const starchPercent = Math.min(dailyNutrition.starch / 2 * 100, 100);
            document.getElementById('proteinPercent').textContent = `${Math.round(proteinPercent)}%`;
            document.getElementById('veggiePercent').textContent = `${Math.round(veggiePercent)}%`;
            document.getElementById('fruitPercent').textContent = `${Math.round(fruitPercent)}%`;
            document.getElementById('starchPercent').textContent = `${Math.round(starchPercent)}%`;
            document.getElementById('proteinBar').style.width = `${proteinPercent}%`;
            document.getElementById('veggieBar').style.width = `${veggiePercent}%`;
            document.getElementById('fruitBar').style.width = `${fruitPercent}%`;
            document.getElementById('starchBar').style.width = `${starchPercent}%`;
        }
        saveMealBtn.addEventListener('click', () => {
            const mealName = document.getElementById('mealName').value;
            const mealTime = document.getElementById('mealTime').value;
            const mealCost = document.getElementById('mealCost').value;
            const mealCalories = document.getElementById('mealCalories').value;
            const mealProtein = parseInt(document.getElementById('mealProtein').value) || 0;
            const mealVeggie = parseInt(document.getElementById('mealVeggie').value) || 0;
            const mealFruit = parseInt(document.getElementById('mealFruit').value) || 0;
            const mealStarch = parseInt(document.getElementById('mealStarch').value) || 0;
            if (!mealName || !mealTime) {
                alert('請填寫餐點名稱和用餐時間');
                return;
            }
            const today = getCurrentDateString();
            const record = {
                date: today,
                mealName,
                mealTime,
                mealCost: mealCost ? parseInt(mealCost) : 0,
                mealCalories: mealCalories ? parseInt(mealCalories) : 0,
                proteinCount: mealProtein,
                veggieCount: mealVeggie,
                fruitCount: mealFruit,
                starchCount: mealStarch,
                timestamp: new Date().getTime()
            };
            const dietRecords = JSON.parse(localStorage.getItem('dietRecords'));
            dietRecords.push(record);
            localStorage.setItem('dietRecords', JSON.stringify(dietRecords));
            // 同時更新今日營養攝取
            const dailyNutrition = JSON.parse(localStorage.getItem('dailyNutrition'));
            dailyNutrition.protein += mealProtein;
            dailyNutrition.veggie += mealVeggie;
            dailyNutrition.fruit += mealFruit;
            dailyNutrition.starch += mealStarch;
            localStorage.setItem('dailyNutrition', JSON.stringify(dailyNutrition));
            updateNutritionDisplay();
            // 重置表單
            document.getElementById('mealName').value = '';
            document.getElementById('mealTime').value = '';
            document.getElementById('mealCost').value = '';
            document.getElementById('mealCalories').value = '';
            document.getElementById('mealProtein').value = '0';
            document.getElementById('mealVeggie').value = '0';
            document.getElementById('mealFruit').value = '0';
            document.getElementById('mealStarch').value = '0';
            alert('飲食紀錄已儲存');
        });
        viewDietRecordsBtn.addEventListener('click', () => {
            const dietRecords = JSON.parse(localStorage.getItem('dietRecords'));
            if (dietRecords.length === 0) {
                showModal('飲食紀錄', '<div class="text-center py-8 text-gray-500">尚無飲食紀錄</div>');
                return;
            }
            // 按日期分組
            const recordsByDate = {};
            dietRecords.forEach((record, idx) => {
                if (!recordsByDate[record.date]) {
                    recordsByDate[record.date] = [];
                }
                // 加入 index 方便刪除
                recordsByDate[record.date].push({...record, _idx: idx});
            });
            // 計算每日平均餐費和總熱量
            const dailyStats = {};
            Object.keys(recordsByDate).forEach(date => {
                const records = recordsByDate[date];
                const totalCost = records.reduce((sum, record) => sum + (record.mealCost || 0), 0);
                const totalCalories = records.reduce((sum, record) => sum + (record.mealCalories || 0), 0);
                dailyStats[date] = {
                    totalCost: totalCost,
                    totalCalories: totalCalories
                };
            });
            // 生成HTML
            let html = '';
            Object.keys(recordsByDate).sort().reverse().forEach(date => {
                const records = recordsByDate[date];
                const totalProtein = records.reduce((sum, record) => sum + (record.proteinCount || 0), 0);
                const totalVeggie = records.reduce((sum, record) => sum + (record.veggieCount || 0), 0);
                const totalFruit = records.reduce((sum, record) => sum + (record.fruitCount || 0), 0);
                const totalStarch = records.reduce((sum, record) => sum + (record.starchCount || 0), 0);
                html += `
                    <div class="mb-6">
                        <div class="flex justify-between items-center mb-2">
                            <h4 class="font-medium">${formatDate(date)}</h4>
                            <div class="text-sm text-gray-500">
                                <div>總花費: ${dailyStats[date].totalCost} 元</div>
                                <div>總熱量: ${dailyStats[date].totalCalories} kcal</div>
                            </div>
                        </div>
                        <div class="flex mb-3">
                            <div class="flex-1 text-center">
                                <div class="text-blue-600 font-bold">${totalProtein}</div>
                                <div class="text-xs text-gray-500">蛋白質</div>
                            </div>
                            <div class="flex-1 text-center">
                                <div class="text-green-600 font-bold">${totalVeggie}</div>
                                <div class="text-xs text-gray-500">蔬菜</div>
                            </div>
                            <div class="flex-1 text-center">
                                <div class="text-yellow-600 font-bold">${totalFruit}</div>
                                <div class="text-xs text-gray-500">水果</div>
                            </div>
                            <div class="flex-1 text-center">
                                <div class="text-orange-600 font-bold">${totalStarch}</div>
                                <div class="text-xs text-gray-500">澱粉</div>
                            </div>
                        </div>
                        <div class="space-y-2">
                `;
                records.sort((a, b) => a.mealTime.localeCompare(b.mealTime)).forEach(record => {
                    html += `
                        <div class="bg-gray-50 p-3 rounded-md record-item flex justify-between items-center">
                            <div>
                                <div class="flex justify-between">
                                    <div class="font-medium">${record.mealName}</div>
                                    <div class="text-sm text-gray-500">${record.mealTime}</div>
                                </div>
                                <div class="text-sm mt-1">
                                    <span class="mr-2">蛋白質: ${record.proteinCount || 0}</span>
                                    <span class="mr-2">蔬菜: ${record.veggieCount || 0}</span>
                                    <span class="mr-2">水果: ${record.fruitCount || 0}</span>
                                    <span>澱粉: ${record.starchCount || 0}</span>
                                </div>
                                <div class="text-sm text-gray-500 mt-1">
                                    ${record.mealCalories ? `熱量: ${record.mealCalories} kcal · ` : ''}
                                    ${record.mealCost ? `花費: ${record.mealCost} 元` : ''}
                                </div>
                            </div>
                            <button class="delete-record-btn text-red-500 ml-2" data-type="diet" data-idx="${record._idx}">刪除</button>
                        </div>
                    `;
                });
                html += `
                        </div>
                    </div>
                `;
            });
            showModal('飲食紀錄', html);
            // 新增事件代理
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('dietRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('dietRecords', JSON.stringify(records));
                    viewDietRecordsBtn.click();
                }
            };
        });
        // --- 飲水分頁 ---
        const addBottleBtn = document.getElementById('addBottleBtn');
        const viewWaterRecordsBtn = document.getElementById('viewWaterRecords');
        const saveBottleBtn = document.getElementById('saveBottle');
        const closeBottleModalBtn = document.getElementById('closeBottleModal');
        function renderBottles() {
            const bottleContainer = document.getElementById('bottleContainer');
            const bottles = JSON.parse(localStorage.getItem('waterBottles'));
            bottleContainer.innerHTML = '';
            if (bottles.length === 0) {
                bottleContainer.innerHTML = `
                    <div class="col-span-2 text-center py-4 text-gray-500">
                        尚未設定水壺，請點擊「新增水壺」按鈕
                    </div>
                `;
                return;
            }
            // 預設顏色組合
            const colorPairs = [
                { start: '#38bdf8', end: '#0ea5e9' },
                { start: '#22d3ee', end: '#06b6d4' },
                { start: '#2dd4bf', end: '#14b8a6' },
                { start: '#4ade80', end: '#22c55e' },
                { start: '#a3e635', end: '#84cc16' },
                { start: '#facc15', end: '#eab308' }
            ];
            bottles.forEach((bottle, index) => {
                const colorIndex = index % colorPairs.length;
                const colorPair = colorPairs[colorIndex];
                const bottleBtn = document.createElement('div');
                bottleBtn.className = 'bottle-btn relative p-4 rounded-lg text-white text-center cursor-pointer transition hover:shadow-lg';
                bottleBtn.style.setProperty('--start-color', colorPair.start);
                bottleBtn.style.setProperty('--end-color', colorPair.end);
                bottleBtn.classList.add('bottle-colors');
                bottleBtn.innerHTML = `
                    <button class="absolute top-1 right-1 text-white opacity-70 hover:opacity-100 p-1" data-bottle-id="${bottle.id}">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                        </svg>
                    </button>
                    <div class="text-2xl mb-1">🥤</div>
                    <div class="font-medium">${bottle.name}</div>
                    <div class="text-sm opacity-80">${bottle.capacity}cc</div>
                `;
                bottleBtn.addEventListener('click', (e) => {
                    if (e.target.closest('button[data-bottle-id]')) {
                        const bottleId = e.target.closest('button[data-bottle-id]').getAttribute('data-bottle-id');
                        deleteBottle(bottleId);
                        return;
                    }
                    recordWaterIntake(bottle);
                });
                bottleContainer.appendChild(bottleBtn);
            });
        }
        addBottleBtn.addEventListener('click', () => {
            document.getElementById('bottleName').value = '';
            document.getElementById('bottleCapacity').value = '';
            document.getElementById('addBottleModal').classList.remove('hidden');
        });
        saveBottleBtn.addEventListener('click', () => {
            const bottleName = document.getElementById('bottleName').value;
            const bottleCapacity = document.getElementById('bottleCapacity').value;
            if (!bottleName || !bottleCapacity) {
                alert('請填寫水壺名稱和容量');
                return;
            }
            const bottles = JSON.parse(localStorage.getItem('waterBottles'));
            bottles.push({
                id: Date.now().toString(),
                name: bottleName,
                capacity: parseInt(bottleCapacity)
            });
            localStorage.setItem('waterBottles', JSON.stringify(bottles));
            document.getElementById('addBottleModal').classList.add('hidden');
            renderBottles();
        });
        closeBottleModalBtn.addEventListener('click', () => {
            document.getElementById('addBottleModal').classList.add('hidden');
        });
        function deleteBottle(bottleId) {
            if (confirm('確定要刪除這個水壺嗎？')) {
                const bottles = JSON.parse(localStorage.getItem('waterBottles'));
                const updatedBottles = bottles.filter(bottle => bottle.id !== bottleId);
                localStorage.setItem('waterBottles', JSON.stringify(updatedBottles));
                renderBottles();
            }
        }
        function recordWaterIntake(bottle) {
            const dailyWater = JSON.parse(localStorage.getItem('dailyWater'));
            dailyWater.intake += bottle.capacity;
            localStorage.setItem('dailyWater', JSON.stringify(dailyWater));
            const waterRecords = JSON.parse(localStorage.getItem('waterRecords'));
            waterRecords.push({
                date: getCurrentDateString(),
                bottleName: bottle.name,
                capacity: bottle.capacity,
                timestamp: new Date().getTime()
            });
            localStorage.setItem('waterRecords', JSON.stringify(waterRecords));
            updateWaterDisplay();
            const waterDrop = document.querySelector('.water-drop');
            waterDrop.classList.remove('water-drop');
            void waterDrop.offsetWidth;
            waterDrop.classList.add('water-drop');
        }
        function updateWaterDisplay() {
            const dailyWater = JSON.parse(localStorage.getItem('dailyWater'));
            const intake = dailyWater.intake;
            const percent = Math.min(intake / 2000 * 100, 100);
            document.getElementById('waterIntake').textContent = intake;
            document.getElementById('waterPercent').textContent = `${Math.round(percent)}%`;
            document.getElementById('waterBar').style.width = `${percent}%`;
        }
        viewWaterRecordsBtn.addEventListener('click', () => {
            const waterRecords = JSON.parse(localStorage.getItem('waterRecords'));
            if (waterRecords.length === 0) {
                showModal('飲水紀錄', '<div class="text-center py-8 text-gray-500">尚無飲水紀錄</div>');
                return;
            }
            const recordsByDate = {};
            waterRecords.forEach((record, idx) => {
                if (!recordsByDate[record.date]) {
                    recordsByDate[record.date] = [];
                }
                recordsByDate[record.date].push({...record, _idx: idx});
            });
            const dailyTotals = {};
            Object.keys(recordsByDate).forEach(date => {
                const records = recordsByDate[date];
                const totalIntake = records.reduce((sum, record) => sum + record.capacity, 0);
                dailyTotals[date] = totalIntake;
            });
            let html = '';
            Object.keys(recordsByDate).sort().reverse().forEach(date => {
                const records = recordsByDate[date];
                const totalIntake = dailyTotals[date];
                const percent = Math.min(totalIntake / 2000 * 100, 100);
                html += `
                    <div class="mb-6">
                        <div class="flex justify-between items-center mb-2">
                            <h4 class="font-medium">${formatDate(date)}</h4>
                            <div class="text-sm text-gray-500">
                                總飲水量: ${totalIntake}cc (${Math.round(percent)}%)
                            </div>
                        </div>
                        <div class="w-full bg-gray-200 rounded-full h-2.5 mb-3">
                            <div class="bg-cyan-500 h-2.5 rounded-full" style="width: ${percent}%"></div>
                        </div>
                        <div class="space-y-2">
                `;
                records.sort((a, b) => a.timestamp - b.timestamp).forEach(record => {
                    const time = new Date(record.timestamp).toLocaleTimeString('zh-TW', { hour: '2-digit', minute: '2-digit' });
                    html += `
                        <div class="bg-gray-50 p-3 rounded-md record-item flex justify-between items-center">
                            <div>
                                <div class="flex justify-between">
                                    <div class="font-medium">${record.bottleName}</div>
                                    <div class="text-sm text-gray-500">${time}</div>
                                </div>
                                <div class="text-sm text-cyan-600 mt-1">
                                    +${record.capacity}cc
                                </div>
                            </div>
                            <button class="delete-record-btn text-red-500 ml-2" data-type="water" data-idx="${record._idx}">刪除</button>
                        </div>
                    `;
                });
                html += `
                        </div>
                    </div>
                `;
            });
            showModal('飲水紀錄', html);
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('waterRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('waterRecords', JSON.stringify(records));
                    viewWaterRecordsBtn.click();
                }
            };
        });
        // --- 運動分頁 ---
        if (!localStorage.getItem('exerciseRecords')) localStorage.setItem('exerciseRecords', JSON.stringify([]));
        const exerciseType = document.getElementById('exerciseType');
        const distanceGroup = document.getElementById('distanceGroup');
        const strengthGroup = document.getElementById('strengthGroup');
        const otherGroup = document.getElementById('otherGroup');
        const addStrengthSetBtn = document.getElementById('addStrengthSet');
        const strengthSetsContainer = document.getElementById('strengthSetsContainer');
        let strengthSetCount = 0;
        exerciseType.addEventListener('change', function() {
            const type = exerciseType.value;
            distanceGroup.classList.add('hidden');
            strengthGroup.classList.add('hidden');
            otherGroup.classList.add('hidden');
            if (["跑步","健走","游泳","騎車"].includes(type)) {
                distanceGroup.classList.remove('hidden');
            } else if (type === '重訓') {
                strengthGroup.classList.remove('hidden');
            } else if (type === '其他') {
                otherGroup.classList.remove('hidden');
            }
        });
        addStrengthSetBtn.addEventListener('click', function() {
            strengthSetCount++;
            const setDiv = document.createElement('div');
            setDiv.className = 'grid grid-cols-5 gap-2 mb-2';
            setDiv.innerHTML = `
                <input type="text" placeholder="主要訓練肌群" class="strength-main w-full px-2 py-1 border rounded" />
                <input type="number" placeholder="單組次數" class="strength-reps w-full px-2 py-1 border rounded" min="1" />
                <input type="number" placeholder="單組重量(kg)" class="strength-weight w-full px-2 py-1 border rounded" min="0" />
                <input type="number" placeholder="總共幾組" class="strength-sets w-full px-2 py-1 border rounded" min="1" />
                <button type="button" class="removeStrengthSet text-red-500">刪除</button>
            `;
            setDiv.querySelector('.removeStrengthSet').onclick = function() {
                setDiv.remove();
            };
            strengthSetsContainer.appendChild(setDiv);
        });
        const saveExerciseBtn = document.getElementById('saveExercise');
        saveExerciseBtn.addEventListener('click', function() {
            const type = exerciseType.value;
            const time = document.getElementById('exerciseTime').value;
            const duration = parseInt(document.getElementById('exerciseDuration').value) || 0;
            let record = { type, time, duration, timestamp: new Date().getTime() };
            if (["跑步","健走","游泳","騎車"].includes(type)) {
                record.distance = parseFloat(document.getElementById('exerciseDistance').value) || 0;
            } else if (type === '重訓') {
                const parts = Array.from(document.querySelectorAll('.strength-part:checked')).map(e=>e.value);
                record.parts = parts;
                record.sets = Array.from(strengthSetsContainer.children).map(setDiv => ({
                    main: setDiv.querySelector('.strength-main').value,
                    reps: parseInt(setDiv.querySelector('.strength-reps').value) || 0,
                    weight: parseFloat(setDiv.querySelector('.strength-weight').value) || 0,
                    sets: parseInt(setDiv.querySelector('.strength-sets').value) || 0
                }));
            } else if (type === '其他') {
                record.note = document.getElementById('exerciseNote').value;
            }
            if (!time || duration <= 0) {
                alert('請填寫運動時間與時長');
                return;
            }
            const exerciseRecords = JSON.parse(localStorage.getItem('exerciseRecords'));
            exerciseRecords.push(record);
            localStorage.setItem('exerciseRecords', JSON.stringify(exerciseRecords));
            // 重置表單
            document.getElementById('exerciseTime').value = '';
            document.getElementById('exerciseDuration').value = '';
            document.getElementById('exerciseDistance').value = '';
            document.getElementById('exerciseNote').value = '';
            Array.from(document.querySelectorAll('.strength-part')).forEach(e=>e.checked=false);
            strengthSetsContainer.innerHTML = '';
            alert('運動紀錄已儲存');
        });
        const viewExerciseRecordsBtn = document.getElementById('viewExerciseRecords');
        viewExerciseRecordsBtn.addEventListener('click', function() {
            const exerciseRecords = JSON.parse(localStorage.getItem('exerciseRecords'));
            if (exerciseRecords.length === 0) {
                showModal('運動紀錄', '<div class="text-center py-8 text-gray-500">尚無運動紀錄</div>');
                return;
            }
            const recordsByDate = {};
            exerciseRecords.forEach((record, idx) => {
                const date = record.time ? record.time.split('T')[0] : '未知日期';
                if (!recordsByDate[date]) recordsByDate[date] = [];
                recordsByDate[date].push({...record, _idx: idx});
            });
            let html = '';
            Object.keys(recordsByDate).sort().reverse().forEach(date => {
                const records = recordsByDate[date];
                html += `<div class="mb-6"><h4 class="font-medium mb-2">${formatDate(date)}</h4><div class="space-y-2">`;
                records.forEach(record => {
                    html += `<div class="bg-gray-50 p-3 rounded-md record-item flex justify-between items-center">`;
                    html += `<div>`;
                    html += `<div class="font-medium mb-1">${record.type}</div>`;
                    html += `<div class="text-sm text-gray-500 mb-1">${record.time ? new Date(record.time).toLocaleTimeString('zh-TW', { hour: '2-digit', minute: '2-digit' }) : ''}，時長: ${record.duration} 分鐘</div>`;
                    if (["跑步","健走","游泳","騎車"].includes(record.type)) {
                        html += `<div class="text-sm">距離: ${record.distance} 公里</div>`;
                    } else if (record.type === '重訓') {
                        html += `<div class="text-sm">訓練部位: ${(record.parts||[]).join('、')}</div>`;
                        if (record.sets && record.sets.length) {
                            html += '<div class="text-xs text-gray-600 mt-1">';
                            record.sets.forEach((set, idx) => {
                                html += `第${idx+1}組：${set.main}，${set.reps}次，${set.weight}kg，${set.sets}組<br/>`;
                            });
                            html += '</div>';
                        }
                    } else if (record.type === '其他') {
                        html += `<div class="text-sm">備註: ${record.note||''}</div>`;
                    }
                    html += `</div>`;
                    html += `<button class="delete-record-btn text-red-500 ml-2" data-type="exercise" data-idx="${record._idx}">刪除</button>`;
                    html += `</div>`;
                });
                html += `</div></div>`;
            });
            showModal('運動紀錄', html);
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('exerciseRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('exerciseRecords', JSON.stringify(records));
                    viewExerciseRecordsBtn.click();
                }
            };
        });
        // --- 生理分頁 ---
        if (!localStorage.getItem('heightWeightRecords')) localStorage.setItem('heightWeightRecords', JSON.stringify([]));
        if (!localStorage.getItem('bodyCompRecords')) localStorage.setItem('bodyCompRecords', JSON.stringify([]));
        if (!localStorage.getItem('bpRecords')) localStorage.setItem('bpRecords', JSON.stringify([]));
        if (!localStorage.getItem('sugarRecords')) localStorage.setItem('sugarRecords', JSON.stringify([]));
        // BMI 自動計算
        const heightInput = document.getElementById('heightInput');
        const weightInput = document.getElementById('weightInput');
        const bmiInput = document.getElementById('bmiInput');
        function updateBMI() {
            const h = parseFloat(heightInput.value);
            const w = parseFloat(weightInput.value);
            if (h > 0 && w > 0) {
                const bmi = w / Math.pow(h/100,2);
                bmiInput.value = bmi.toFixed(1);
            } else {
                bmiInput.value = '';
            }
        }
        heightInput.addEventListener('input', updateBMI);
        weightInput.addEventListener('input', updateBMI);
        // 儲存身高體重
        const saveHeightWeightBtn = document.getElementById('saveHeightWeight');
        saveHeightWeightBtn.addEventListener('click', function() {
            const h = parseFloat(heightInput.value);
            const w = parseFloat(weightInput.value);
            const bmi = parseFloat(bmiInput.value);
            if (!h || !w || !bmi) {
                alert('請填寫身高與體重');
                return;
            }
            const records = JSON.parse(localStorage.getItem('heightWeightRecords'));
            records.push({ height: h, weight: w, bmi: bmi, time: new Date().toISOString() });
            localStorage.setItem('heightWeightRecords', JSON.stringify(records));
            heightInput.value = '';
            weightInput.value = '';
            bmiInput.value = '';
            alert('紀錄已儲存');
        });
        // 查看身高體重紀錄
        const viewHeightWeightRecordsBtn = document.getElementById('viewHeightWeightRecords');
        viewHeightWeightRecordsBtn.addEventListener('click', function() {
            const records = JSON.parse(localStorage.getItem('heightWeightRecords'));
            if (records.length === 0) {
                showModal('身高體重紀錄', '<div class="text-center py-8 text-gray-500">尚無紀錄</div>');
                return;
            }
            let html = '<div class="space-y-2">';
            records.slice().reverse().forEach((r, idx) => {
                const realIdx = records.length - 1 - idx;
                html += `<div class='bg-gray-50 p-3 rounded-md record-item flex justify-between items-center'>`;
                html += `<div>`;
                html += `<div>時間：${new Date(r.time).toLocaleString('zh-TW')}</div>`;
                html += `<div>身高：${r.height} cm，體重：${r.weight} kg，BMI：${r.bmi}</div>`;
                html += `</div>`;
                html += `<button class="delete-record-btn text-red-500 ml-2" data-type="heightWeight" data-idx="${realIdx}">刪除</button>`;
                html += `</div>`;
            });
            html += '</div>';
            showModal('身高體重紀錄', html);
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('heightWeightRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('heightWeightRecords', JSON.stringify(records));
                    viewHeightWeightRecordsBtn.click();
                }
            };
        });
        // 儲存體脂/內臟脂肪/肌肉率
        const saveBodyCompBtn = document.getElementById('saveBodyComp');
        saveBodyCompBtn.addEventListener('click', function() {
            const fat = parseFloat(document.getElementById('fatInput').value);
            const vis = parseFloat(document.getElementById('visFatInput').value);
            const muscle = parseFloat(document.getElementById('muscleInput').value);
            if (!fat && !vis && !muscle) {
                alert('請填寫至少一項');
                return;
            }
            const records = JSON.parse(localStorage.getItem('bodyCompRecords'));
            records.push({ fat, vis, muscle, time: new Date().toISOString() });
            localStorage.setItem('bodyCompRecords', JSON.stringify(records));
            document.getElementById('fatInput').value = '';
            document.getElementById('visFatInput').value = '';
            document.getElementById('muscleInput').value = '';
            alert('紀錄已儲存');
        });
        // 查看體脂/內臟脂肪/肌肉率紀錄
        const viewBodyCompRecordsBtn = document.getElementById('viewBodyCompRecords');
        viewBodyCompRecordsBtn.addEventListener('click', function() {
            const records = JSON.parse(localStorage.getItem('bodyCompRecords'));
            if (records.length === 0) {
                showModal('體脂/內臟脂肪/肌肉率紀錄', '<div class="text-center py-8 text-gray-500">尚無紀錄</div>');
                return;
            }
            let html = '<div class="space-y-2">';
            records.slice().reverse().forEach((r, idx) => {
                const realIdx = records.length - 1 - idx;
                html += `<div class='bg-gray-50 p-3 rounded-md record-item flex justify-between items-center'>`;
                html += `<div>`;
                html += `<div>時間：${new Date(r.time).toLocaleString('zh-TW')}</div>`;
                html += `<div>體脂率：${r.fat||'-'}%，內臟脂肪：${r.vis||'-'}，肌肉率：${r.muscle||'-'}%</div>`;
                html += `</div>`;
                html += `<button class="delete-record-btn text-red-500 ml-2" data-type="bodyComp" data-idx="${realIdx}">刪除</button>`;
                html += `</div>`;
            });
            html += '</div>';
            showModal('體脂/內臟脂肪/肌肉率紀錄', html);
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('bodyCompRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('bodyCompRecords', JSON.stringify(records));
                    viewBodyCompRecordsBtn.click();
                }
            };
        });
        // 儲存血壓
        const saveBPBtn = document.getElementById('saveBP');
        saveBPBtn.addEventListener('click', function() {
            const sys = parseInt(document.getElementById('sysBPInput').value);
            const dia = parseInt(document.getElementById('diaBPInput').value);
            if (!sys || !dia) {
                alert('請填寫收縮壓與舒張壓');
                return;
            }
            const records = JSON.parse(localStorage.getItem('bpRecords'));
            records.push({ sys, dia, time: new Date().toISOString() });
            localStorage.setItem('bpRecords', JSON.stringify(records));
            document.getElementById('sysBPInput').value = '';
            document.getElementById('diaBPInput').value = '';
            alert('紀錄已儲存');
        });
        // 查看血壓紀錄
        const viewBPRecordsBtn = document.getElementById('viewBPRecords');
        viewBPRecordsBtn.addEventListener('click', function() {
            const records = JSON.parse(localStorage.getItem('bpRecords'));
            if (records.length === 0) {
                showModal('血壓紀錄', '<div class="text-center py-8 text-gray-500">尚無紀錄</div>');
                return;
            }
            let html = '<div class="space-y-2">';
            records.slice().reverse().forEach((r, idx) => {
                const realIdx = records.length - 1 - idx;
                html += `<div class='bg-gray-50 p-3 rounded-md record-item flex justify-between items-center'>`;
                html += `<div>`;
                html += `<div>時間：${new Date(r.time).toLocaleString('zh-TW')}</div>`;
                html += `<div>收縮壓：${r.sys}，舒張壓：${r.dia}</div>`;
                html += `</div>`;
                html += `<button class="delete-record-btn text-red-500 ml-2" data-type="bp" data-idx="${realIdx}">刪除</button>`;
                html += `</div>`;
            });
            html += '</div>';
            showModal('血壓紀錄', html);
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('bpRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('bpRecords', JSON.stringify(records));
                    viewBPRecordsBtn.click();
                }
            };
        });
        // 儲存血糖
        const saveSugarBtn = document.getElementById('saveSugar');
        saveSugarBtn.addEventListener('click', function() {
            const fasting = parseInt(document.getElementById('sugarFastingInput').value);
            const after = parseInt(document.getElementById('sugarAfterInput').value);
            if (!fasting && !after) {
                alert('請填寫至少一項血糖值');
                return;
            }
            const records = JSON.parse(localStorage.getItem('sugarRecords'));
            records.push({ fasting, after, time: new Date().toISOString() });
            localStorage.setItem('sugarRecords', JSON.stringify(records));
            document.getElementById('sugarFastingInput').value = '';
            document.getElementById('sugarAfterInput').value = '';
            alert('紀錄已儲存');
        });
        // 查看血糖紀錄
        const viewSugarRecordsBtn = document.getElementById('viewSugarRecords');
        viewSugarRecordsBtn.addEventListener('click', function() {
            const records = JSON.parse(localStorage.getItem('sugarRecords'));
            if (records.length === 0) {
                showModal('血糖紀錄', '<div class="text-center py-8 text-gray-500">尚無紀錄</div>');
                return;
            }
            let html = '<div class="space-y-2">';
            records.slice().reverse().forEach((r, idx) => {
                const realIdx = records.length - 1 - idx;
                html += `<div class='bg-gray-50 p-3 rounded-md record-item flex justify-between items-center'>`;
                html += `<div>`;
                html += `<div>時間：${new Date(r.time).toLocaleString('zh-TW')}</div>`;
                html += `<div>空腹/飯前：${r.fasting||'-'}，飯後：${r.after||'-'}</div>`;
                html += `</div>`;
                html += `<button class="delete-record-btn text-red-500 ml-2" data-type="sugar" data-idx="${realIdx}">刪除</button>`;
                html += `</div>`;
            });
            html += '</div>';
            showModal('血糖紀錄', html);
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('sugarRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('sugarRecords', JSON.stringify(records));
                    viewSugarRecordsBtn.click();
                }
            };
        });
        // --- 睡眠分頁 ---
        if (!localStorage.getItem('sleepRecords')) localStorage.setItem('sleepRecords', JSON.stringify([]));
        const sleepDateInput = document.getElementById('sleepDate');
        function setTodaySleepDate() {
            const today = new Date();
            sleepDateInput.value = today.toISOString().split('T')[0];
        }
        setTodaySleepDate();
        const saveSleepBtn = document.getElementById('saveSleep');
        saveSleepBtn.addEventListener('click', function() {
            const date = sleepDateInput.value;
            const start = document.getElementById('sleepStart').value;
            const end = document.getElementById('sleepEnd').value;
            const quality = parseInt(document.getElementById('sleepQuality').value);
            if (!start || !end) {
                alert('請填寫就寢與起床時間');
                return;
            }
            // 計算時長（可跨日）
            const [sh, sm] = start.split(':').map(Number);
            const [eh, em] = end.split(':').map(Number);
            let startMins = sh * 60 + sm;
            let endMins = eh * 60 + em;
            let duration = endMins - startMins;
            if (duration <= 0) duration += 24 * 60;
            const hours = Math.floor(duration / 60);
            const mins = duration % 60;
            const records = JSON.parse(localStorage.getItem('sleepRecords'));
            records.push({ date, start, end, duration, quality, time: new Date().toISOString() });
            localStorage.setItem('sleepRecords', JSON.stringify(records));
            document.getElementById('sleepStart').value = '';
            document.getElementById('sleepEnd').value = '';
            document.getElementById('sleepQuality').value = '3';
            alert(`睡眠紀錄已儲存\n時長：${hours}小時${mins}分鐘`);
        });
        const viewSleepRecordsBtn = document.getElementById('viewSleepRecords');
        viewSleepRecordsBtn.addEventListener('click', function() {
            const records = JSON.parse(localStorage.getItem('sleepRecords'));
            if (records.length === 0) {
                showModal('睡眠紀錄', '<div class="text-center py-8 text-gray-500">尚無睡眠紀錄</div>');
                return;
            }
            const today = new Date();
            const last7 = [];
            for (let i = 0; i < 7; i++) {
                const d = new Date(today);
                d.setDate(today.getDate() - i);
                last7.push(d.toISOString().split('T')[0]);
            }
            let totalMins = 0, count = 0;
            last7.forEach(date => {
                const rec = records.filter(r => r.date === date);
                if (rec.length) {
                    const max = Math.max(...rec.map(r=>r.duration));
                    totalMins += max;
                    count++;
                }
            });
            let avgStr = '';
            if (count) {
                const avg = totalMins / count;
                const h = Math.floor(avg / 60);
                const m = Math.round(avg % 60);
                avgStr = `<div class='mb-2 text-blue-700 font-medium'>過去七天平均睡眠時長：${h}小時${m}分鐘</div>`;
            }
            let html = avgStr + '<div class="space-y-2">';
            records.slice().reverse().forEach((r, idx) => {
                const realIdx = records.length - 1 - idx;
                const h = Math.floor(r.duration / 60);
                const m = r.duration % 60;
                html += `<div class='bg-gray-50 p-3 rounded-md record-item flex justify-between items-center'>`;
                html += `<div>`;
                html += `<div>日期：${r.date}，時長：${h}小時${m}分鐘</div>`;
                html += `<div>就寢：${r.start}，起床：${r.end}</div>`;
                html += `<div>睡眠品質：${r.quality} 分</div>`;
                html += `</div>`;
                html += `<button class="delete-record-btn text-red-500 ml-2" data-type="sleep" data-idx="${realIdx}">刪除</button>`;
                html += `</div>`;
            });
            html += '</div>';
            showModal('睡眠紀錄', html);
            document.getElementById('modalContent').onclick = function(e) {
                if (e.target.classList.contains('delete-record-btn')) {
                    const idx = parseInt(e.target.getAttribute('data-idx'));
                    const records = JSON.parse(localStorage.getItem('sleepRecords'));
                    records.splice(idx, 1);
                    localStorage.setItem('sleepRecords', JSON.stringify(records));
                    viewSleepRecordsBtn.click();
                }
            };
        });
        // --- 工具 ---
        function formatDate(dateString) {
            const date = new Date(dateString);
            return date.toLocaleDateString('zh-TW', { month: 'long', day: 'numeric' });
        }
        // 每分鐘自動檢查日期是否變更，確保每日歸零
        setInterval(checkAndUpdateDailyData, 60 * 1000);
    });
    </script>
</body>
</html> 
