[toeic_timer.html](https://github.com/user-attachments/files/22712914/toeic_timer.html)# 🎯 多益專注計時器 (TOEIC Focus Timer)

這是一個專為多益 (TOEIC) 考生設計的網頁計時器，旨在幫助使用者在固定的時間內保持專注，同時追蹤距離考試日期與目標分數，提升學習效率。

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)

---

## ✨ Demo & 截圖


**線上體驗網址 👉 [點我前往體驗](https://your-github-username.github.io/toeic-study-timer/)**

<br>

**專案截圖**
[Uploading <!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>多益英文學習計時器1006</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Use Inter font from Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
            background-color: #f3f4f6;
        }
        .timer-button:active {
            transform: translateY(2px);
            box-shadow: 0 0px 0px rgba(0, 0, 0, 0.2);
        }
        #pause-button.active {
            background-color: #f97316;
            transform: translateY(2px);
            box-shadow: 0 0px 0px rgba(0, 0, 0, 0.2);
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen text-gray-800">
    <div class="bg-white p-8 md:p-12 rounded-2xl shadow-2xl max-w-lg w-full text-center m-4">
        <h1 class="text-3xl md:text-4xl font-bold mb-2 text-indigo-700">多益英文學習計時器</h1>
        <p class="text-gray-500 mb-6">為你的多益考試做準備！</p>
        
        <!-- TOEIC Exam Countdown -->
        <div class="mb-6 bg-indigo-50 p-4 rounded-xl">
            <p class="text-lg font-medium text-gray-600">距離考試還剩下</p>
            <p id="exam-countdown" class="text-2xl font-bold text-indigo-700 mt-1">...</p>
        </div>

        <!-- Score Section -->
        <div class="flex flex-col md:flex-row justify-center gap-6 mb-8">
            <div class="w-full">
                <label for="current-score" class="block text-sm font-medium text-gray-700 mb-1">目前分數</label>
                <input type="number" id="current-score" value="500" class="w-full text-center p-3 rounded-lg border-2 border-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-500 transition-all duration-300">
            </div>
            <div class="w-full">
                <label for="target-score" class="block text-sm font-medium text-gray-700 mb-1">目標分數</label>
                <input type="number" id="target-score" value="770" class="w-full text-center p-3 rounded-lg border-2 border-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-500 transition-all duration-300">
            </div>
        </div>

        <!-- Timer Display -->
        <div class="relative mb-6">
            <p id="timer-display" class="text-6xl md:text-7xl font-extrabold text-indigo-700 transition-colors duration-500">30:00</p>
            <p id="timer-message" class="absolute inset-x-0 bottom-[-3rem] text-lg font-semibold text-red-500 opacity-0 transition-opacity duration-300 pointer-events-none">
                30分鐘已到，要休息囉！
            </p>
        </div>

        <!-- Control Buttons -->
        <div class="flex flex-col sm:flex-row justify-center space-y-4 sm:space-y-0 sm:space-x-4">
            <button id="start-button" class="timer-button w-full sm:w-auto px-6 py-3 rounded-xl bg-indigo-500 text-white font-bold text-lg shadow-md hover:bg-indigo-600 transition-all duration-300">
                開始
            </button>
            <button id="pause-button" class="timer-button w-full sm:w-auto px-6 py-3 rounded-xl bg-orange-500 text-white font-bold text-lg shadow-md hover:bg-orange-600 transition-all duration-300">
                暫停
            </button>
            <button id="reset-button" class="timer-button w-full sm:w-auto px-6 py-3 rounded-xl bg-gray-500 text-white font-bold text-lg shadow-md hover:bg-gray-600 transition-all duration-300">
                重置
            </button>
        </div>

        <!-- Gemini API Integration Section -->
        <div id="gemini-section" class="mt-12 opacity-0 transition-opacity duration-500 pointer-events-none">
            <button id="generate-button" class="w-full px-6 py-3 rounded-xl bg-purple-600 text-white font-bold text-lg shadow-md hover:bg-purple-700 transition-all duration-300">
                ✨ 產生多益練習題 ✨
            </button>
            <div id="question-container" class="mt-6 text-left p-6 rounded-xl border-2 border-gray-200">
                <p id="question-text" class="text-gray-900 font-medium mb-4">點擊上方按鈕來產生練習題...</p>
                <div id="options-container" class="space-y-2"></div>
                <button id="show-answer-button" class="mt-4 px-4 py-2 rounded-lg bg-gray-200 text-gray-800 font-bold hover:bg-gray-300 transition-colors duration-200 hidden">顯示答案</button>
                <p id="answer-explanation" class="mt-4 text-sm text-green-700 font-semibold hidden"></p>
            </div>
        </div>
    </div>

    <script>
        const examDateElement = document.getElementById('exam-countdown');
        const timerDisplay = document.getElementById('timer-display');
        const startButton = document.getElementById('start-button');
        const pauseButton = document.getElementById('pause-button');
        const resetButton = document.getElementById('reset-button');
        const timerMessage = document.getElementById('timer-message');
        const geminiSection = document.getElementById('gemini-section');
        const generateButton = document.getElementById('generate-button');
        const questionContainer = document.getElementById('question-container');
        const questionText = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const showAnswerButton = document.getElementById('show-answer-button');
        const answerExplanation = document.getElementById('answer-explanation');

        let timerInterval;
        let timeLeft = 30 * 60; // 30 minutes in seconds
        let isRunning = false;
        let isPaused = false;
        let correctAnswer = '';

        // Function to calculate and display days until the exam
        function updateExamCountdown() {
            const examDate = new Date('2025-12-28T00:00:00');
            const now = new Date();
            const difference = examDate.getTime() - now.getTime();
            const daysLeft = Math.ceil(difference / (1000 * 60 * 60 * 24));

            if (daysLeft > 0) {
                examDateElement.textContent = `${daysLeft} 天`;
            } else {
                examDateElement.textContent = '考試已經結束囉！';
            }
        }

        // Function to format time and update the display
        function updateTimerDisplay() {
            const minutes = Math.floor(timeLeft / 60);
            const seconds = timeLeft % 60;
            const formattedTime = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
            timerDisplay.textContent = formattedTime;
        }

        // Function to start the timer
        function startTimer() {
            if (isRunning) return; // Prevent multiple intervals
            isRunning = true;
            isPaused = false;
            pauseButton.classList.remove('active');
            timerMessage.style.opacity = '0'; // Hide the message
            geminiSection.style.opacity = '0'; // Hide Gemini section
            geminiSection.style.pointerEvents = 'none';
            resetQuestionDisplay();
            
            timerInterval = setInterval(() => {
                if (timeLeft > 0) {
                    timeLeft--;
                    updateTimerDisplay();
                } else {
                    clearInterval(timerInterval);
                    isRunning = false;
                    timerMessage.style.opacity = '1'; // Show the message
                    geminiSection.style.opacity = '1'; // Show Gemini section
                    geminiSection.style.pointerEvents = 'auto';
                    // Disable start and pause buttons after timer finishes
                    startButton.disabled = true;
                    pauseButton.disabled = true;
                    startButton.style.opacity = 0.5;
                    pauseButton.style.opacity = 0.5;
                }
            }, 1000);
            
            // Re-enable buttons if they were disabled
            startButton.disabled = false;
            pauseButton.disabled = false;
            startButton.style.opacity = 1;
            pauseButton.style.opacity = 1;
        }

        // Function to pause the timer
        function pauseTimer() {
            if (!isRunning && !isPaused) return; // Only pause if the timer is running or was paused
            isPaused = !isPaused;
            if (isPaused) {
                clearInterval(timerInterval);
                isRunning = false; // 修復: 當計時器暫停時，將 isRunning 狀態設為 false
                pauseButton.classList.add('active');
            } else {
                startTimer(); // Resume the timer, this call will now work correctly
                pauseButton.classList.remove('active');
            }
        }

        // Function to reset the timer
        function resetTimer() {
            clearInterval(timerInterval);
            isRunning = false;
            isPaused = false;
            timeLeft = 30 * 60;
            updateTimerDisplay();
            pauseButton.classList.remove('active');
            timerMessage.style.opacity = '0'; // Hide the message
            geminiSection.style.opacity = '0'; // Hide Gemini section
            geminiSection.style.pointerEvents = 'none';
            resetQuestionDisplay();
            // Re-enable buttons
            startButton.disabled = false;
            pauseButton.disabled = false;
            startButton.style.opacity = 1;
            pauseButton.style.opacity = 1;
        }

        // Resets the question display
        function resetQuestionDisplay() {
            questionText.textContent = '點擊上方按鈕來產生練習題...';
            optionsContainer.innerHTML = '';
            showAnswerButton.classList.add('hidden');
            answerExplanation.classList.add('hidden');
            answerExplanation.textContent = '';
        }

        // Function to fetch a TOEIC question from the Gemini API
        async function generateToeicQuestion() {
            // Show loading state
            questionText.textContent = '正在為你產生練習題...';
            optionsContainer.innerHTML = '';
            showAnswerButton.classList.add('hidden');
            answerExplanation.classList.add('hidden');
            
            const prompt = `Act as a TOEIC test creator. Provide a single, TOEIC-style grammar or vocabulary question. The question should be a short sentence with a single blank, followed by four multiple-choice options (A, B, C, D) and a detailed explanation of the correct answer. The output MUST be a JSON object and NOTHING else.
                The JSON schema must be: {
                    "question": "string",
                    "options": {
                        "A": "string",
                        "B": "string",
                        "C": "string",
                        "D": "string"
                    },
                    "answer": "string",
                    "explanation": "string"
                }`;
            
            const apiKey = "";
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;

            const payload = {
                contents: [{ parts: [{ text: prompt }] }],
                generationConfig: {
                    responseMimeType: "application/json",
                    responseSchema: {
                        type: "OBJECT",
                        properties: {
                            "question": { "type": "STRING" },
                            "options": {
                                "type": "OBJECT",
                                "properties": {
                                    "A": { "type": "STRING" },
                                    "B": { "type": "STRING" },
                                    "C": { "type": "STRING" },
                                    "D": { "type": "STRING" }
                                }
                            },
                            "answer": { "type": "STRING" },
                            "explanation": { "type": "STRING" }
                        },
                        "required": ["question", "options", "answer", "explanation"]
                    }
                }
            };
            
            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                const result = await response.json();
                
                if (result.candidates && result.candidates.length > 0 && result.candidates[0].content && result.candidates[0].content.parts && result.candidates[0].content.parts.length > 0) {
                    const jsonString = result.candidates[0].content.parts[0].text;
                    const parsedQuestion = JSON.parse(jsonString);

                    // Update UI with the new question
                    questionText.textContent = parsedQuestion.question;
                    optionsContainer.innerHTML = '';
                    correctAnswer = parsedQuestion.answer;
                    answerExplanation.textContent = parsedQuestion.explanation;

                    // Display options
                    for (const key in parsedQuestion.options) {
                        const optionElement = document.createElement('div');
                        optionElement.className = 'p-3 rounded-lg border-2 border-gray-300 hover:bg-gray-100 cursor-pointer transition-colors duration-200';
                        optionElement.innerHTML = `<span class="font-bold mr-2">${key}.</span> ${parsedQuestion.options[key]}`;
                        optionElement.addEventListener('click', () => handleOptionClick(key, parsedQuestion.answer));
                        optionsContainer.appendChild(optionElement);
                    }
                    showAnswerButton.classList.remove('hidden');

                } else {
                    questionText.textContent = '無法產生練習題，請稍後再試。';
                }
            } catch (error) {
                console.error('Error generating question:', error);
                questionText.textContent = '產生練習題時發生錯誤，請檢查連線或稍後再試。';
            }
        }

        // Handle user clicking an option
        function handleOptionClick(selectedOption, answer) {
            const options = optionsContainer.querySelectorAll('div');
            options.forEach(option => {
                option.classList.remove('border-green-500', 'border-red-500', 'bg-green-100', 'bg-red-100');
                option.style.pointerEvents = 'none'; // Disable clicking after selection
                const optionLetter = option.textContent.trim().substring(0, 1);
                if (optionLetter === answer) {
                    option.classList.add('border-green-500', 'bg-green-100');
                } else if (optionLetter === selectedOption) {
                    option.classList.add('border-red-500', 'bg-red-100');
                }
            });
            answerExplanation.classList.remove('hidden');
        }

        // Add event listeners to the buttons
        startButton.addEventListener('click', startTimer);
        pauseButton.addEventListener('click', pauseTimer);
        resetButton.addEventListener('click', resetTimer);
        generateButton.addEventListener('click', generateToeicQuestion);
        showAnswerButton.addEventListener('click', () => {
            const options = optionsContainer.querySelectorAll('div');
            options.forEach(option => {
                option.classList.remove('border-green-500', 'border-red-500', 'bg-green-100', 'bg-red-100');
                const optionLetter = option.textContent.trim().substring(0, 1);
                if (optionLetter === correctAnswer) {
                    option.classList.add('border-green-500', 'bg-green-100');
                }
            });
            answerExplanation.classList.remove('hidden');
        });

        // Initial setup on page load
        document.addEventListener('DOMContentLoaded', () => {
            updateExamCountdown();
            updateTimerDisplay();
        });
    </script>
</body>
</html>
toeic_timer.html…]()




---

## 🚀 主要功能 (Features)

* **📅 考試倒數**：自動計算並顯示距離指定多益考試日期 (2025/11/30) 的剩餘天數。
* **📊 分數追蹤**：可自行輸入目前分數 (預設 500) 與目標分數 (預設 770)，時刻提醒學習目標。
* **⏲️ 專注計時**：核心的 30 分鐘倒數計時器，幫助你實踐番茄工作法 (Pomodoro Technique)。
* **⏯️ 完整控制**：提供「開始」、「暫停」、「重置」功能。暫停時按鈕會有視覺反饋效果，讓使用者清楚知道當前狀態。
* **🔔 完成提醒**：計時結束後，會在下方顯示「30分鐘已到，要休息囉！」的溫馨提示文字。
* **🔄 循環運作**：點擊「重置」後，計時器會立刻回到 30:00，提示文字消失，可以無縫開始下一個專注循環。

---

## 🛠️ 使用技術 (Tech Stack)

* **HTML5**: 負責網頁的基礎架構。
* **CSS3**: 負責所有視覺樣式與按鈕的互動效果。
* **JavaScript (ES6+)**: 處理所有核心邏輯，包括倒數計時、日期計算、事件監聽等。

---

## ⚙️ 如何在本地端安裝與啟動 (Getting Started)

如果你想在自己的電腦上運行這個專案，請依照以下步驟：

**1. 複製這個儲存庫 (Repository)**
```bash
git clone [https://github.com/seanchiu89757/toeic-study-timer.git](https://github.com/seanchiu89757/toeic-study-timer.git)
