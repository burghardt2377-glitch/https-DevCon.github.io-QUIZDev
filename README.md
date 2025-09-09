<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Квиз: Угадай застройщика по фото</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            color: #333;
        }
        #quiz-container {
            background-color: white;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
            width: 90%;
            max-width: 600px;
            padding: 25px;
            text-align: center;
            box-sizing: border-box;
        }
        .hide {
            display: none;
        }
        .btn {
            background-color: #4b6cb7;
            color: white;
            border: none;
            padding: 12px 20px;
            margin: 8px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            transition: all 0.3s ease;
            width: calc(100% - 16px);
        }
        .btn:hover {
            background-color: #3a5999;
            transform: translateY(-2px);
        }
        #image-container {
            width: 100%;
            height: 250px;
            margin: 15px 0;
            border-radius: 10px;
            overflow: hidden;
        }
        #quiz-image {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        #progress {
            font-size: 14px;
            margin-bottom: 10px;
            color: #666;
        }
        #score {
            font-size: 18px;
            font-weight: bold;
            margin: 15px 0;
        }
        .feedback.correct { color: #2ecc71; font-weight: bold; }
        .feedback.incorrect { color: #e74c3c; font-weight: bold; }
        #result-text {
            font-size: 22px;
            margin: 20px 0;
        }
        #share-container {
            margin-top: 20px;
        }
        #share-link {
            width: calc(100% - 20px);
            padding: 10px;
            border: 1px dashed #ccc;
            border-radius: 5px;
            background-color: #f9f9f9;
            cursor: copy;
            margin-bottom: 10px;
        }
    </style>
</head>
<body>

    <div id="quiz-container">
        <!-- Экран начала игры -->
        <div id="start-screen">
            <h1>Угадай застройщика по фото</h1>
            <p>Тебя ждут 10 вопросов. Смотри на фото жилого комплекса и выбирай, какой компании он принадлежит. Справишься?</p>
            <button id="start-btn" class="btn">Начать игру!</button>
        </div>

        <!-- Экран с вопросом -->
        <div id="game-screen" class="hide">
            <div id="progress">Вопрос <span id="current-question">1</span>/<span id="total-questions">10</span></div>
            <div id="image-container">
                <img id="quiz-image" src="" alt="Жилой комплекс">
            </div>
            <div id="options-container"></div>
            <div id="feedback" class="feedback"></div>
            <div id="score">Правильных ответов: <span id="score-value">0</span></div>
        </div>

        <!-- Экран с результатами -->
        <div id="result-screen" class="hide">
            <h2>Твой результат!</h2>
            <div id="result-text">Ты угадал <span id="final-score">0</span> из <span id="final-total">10</span> застройщиков!</div>
            <button id="restart-btn" class="btn">Сыграть ещё раз</button>
            <div id="share-container">
                <p>Поделись результатом:</p>
                <input type="text" id="share-link" value="https://virool.github.io/quiz-real-estate/" readonly>
                <button onclick="copyLink()" class="btn">Скопировать ссылку</button>
            </div>
        </div>
    </div>

    <script>
        // Массив с данными для квиза: ссылка на изображение и правильный ответ
        const quizData = [
            {
                image: "https://s10.stc.yc.kpcdn.net/share/i/12/13207776/de-1200.jpg",
                correctAnswer: "ГК АПРИ",
                options: ["ГК АПРИ", "Этажи", "Capital Group", "СЗ 4Д"]
            },
            {
                image: "https://mir-s3-cdn-cf.behance.net/project_modules/max_3840/e07568169018327.6445b9d9d35bf.jpg",
                correctAnswer: "Донстрой",
                options: ["Ленстройтрест", "Донстрой", "Гранель", "Glorax"]
            },
            {
                image: "https://novostroika78.ru/assets/cities/spb/houses/sk-etalon-lenspeczsmu/zhk-galaktika-pro/1-2.jpg",
                correctAnswer: "ГК Эталон",
                options: ["ГК Эталон", "ГК Основа", "КОРТРОС", "Sminex"]
            },
            {
                image: "https://storage.myseldon.com/news-pict-74/743CA638192C57378ECA417CC7E3EB44",
                correctAnswer: "Capital Group",
                options: ["Capital Group", "Авагрупп", "ГК КВС", "ГК ВЕКТОР"]
            },
            {
                image: "https://cdn.bre.ru/storage/8a/8b/8a8b3c8c11e882a5f1c0e5a8c2f1c7f.jpg",
                correctAnswer: "Sminex",
                options: ["Пионер", "Sminex", "ГК Родина", "АК Барс Дом"]
            },
            {
                image: "https://i.archi.ru/i/460759.jpg",
                correctAnswer: "КОРТРОС",
                options: ["КОРТРОС", "Главстрой", "Неометрия", "October Group"]
            },
            {
                image: "hhttps://www.gor-stroy.com/templates/yootheme/cache/on_moscow-11c004fb.jpeg",
                correctAnswer: "Главстрой",
                options: ["Главстрой", "ГК Инсити", "Унистрой", "DARS"]
            },
            {
                image: "https://novostroy-78.ru/img/complexes/6512922825b1f474d846ac96b29a3f45.jpg",
                correctAnswer: "Ленстройтрест",
                options: ["Ленстройтрест", "Tashir", "Prime Life Development", "АКВИЛОН НЕДВИЖИМОСТЬ"]
            },
            {
                image: "https://avatars.mds.yandex.net/get-promoarticles/6426355/pub_666fe9ff9d5bcc699c5e96e4_6670305ce771314361cb1e72/scale_2400",
                correctAnswer: "Пионер",
                options: ["Пионер", "Рекомендуем недвижимость", "Садовое кольцо", "BAZA"]
            },
            {
                image: "https://proplanirovki.ru/storage/blocks/1fc9b6f4-e3d8-4e49-a56c-b4d9495a9f9b.jpg",
                correctAnswer: "ГК Основа",
                options: ["ГК Основа", "Отрада Девелопмент", "RES Development", "Stonehedge"]
            }
        ];

        // Элементы DOM
        const startScreen = document.getElementById('start-screen');
        const gameScreen = document.getElementById('game-screen');
        const resultScreen = document.getElementById('result-screen');
        const startBtn = document.getElementById('start-btn');
        const restartBtn = document.getElementById('restart-btn');
        const quizImage = document.getElementById('quiz-image');
        const optionsContainer = document.getElementById('options-container');
        const feedbackEl = document.getElementById('feedback');
        const currentQuestionEl = document.getElementById('current-question');
        const totalQuestionsEl = document.getElementById('total-questions');
        const scoreValueEl = document.getElementById('score-value');
        const finalScoreEl = document.getElementById('final-score');
        const finalTotalEl = document.getElementById('final-total');

        // Переменные игры
        let currentQuestion = 0;
        let score = 0;
        totalQuestionsEl.textContent = quizData.length;

        // Запуск игры
        startBtn.addEventListener('click', startGame);
        restartBtn.addEventListener('click', startGame);

        function startGame() {
            currentQuestion = 0;
            score = 0;
            scoreValueEl.textContent = score;
            startScreen.classList.add('hide');
            resultScreen.classList.add('hide');
            gameScreen.classList.remove('hide');
            feedbackEl.textContent = '';
            loadQuestion();
        }

        function loadQuestion() {
            if (currentQuestion >= quizData.length) {
                showResult();
                return;
            }
            resetState();
            const question = quizData[currentQuestion];
            currentQuestionEl.textContent = currentQuestion + 1;
            quizImage.src = question.image;
            quizImage.alt = `ЖК от ${question.correctAnswer}`;

            // Перемешиваем варианты ответов для каждого вопроса
            const shuffledOptions = [...question.options].sort(() => Math.random() - 0.5);

            shuffledOptions.forEach(option => {
                const button = document.createElement('button');
                button.textContent = option;
                button.classList.add('btn');
                button.addEventListener('click', () => selectAnswer(option, question.correctAnswer));
                optionsContainer.appendChild(button);
            });
        }

        function resetState() {
            feedbackEl.textContent = '';
            feedbackEl.className = 'feedback';
            while (optionsContainer.firstChild) {
                optionsContainer.firstChild.remove();
            }
        }

        function selectAnswer(selectedOption, correctAnswer) {
            // Блокируем все кнопки после выбора
            const allButtons = optionsContainer.querySelectorAll('.btn');
            allButtons.forEach(btn => {
                btn.disabled = true;
            });

            // Проверяем ответ
            if (selectedOption === correctAnswer) {
                feedbackEl.textContent = 'Верно! 👍';
                feedbackEl.classList.add('correct');
                score++;
                scoreValueEl.textContent = score;
            } else {
                feedbackEl.textContent = `Нет! Это: ${correctAnswer}`;
                feedbackEl.classList.add('incorrect');
            }

            // Ждем 1.5 секунды и загружаем следующий вопрос
            setTimeout(() => {
                currentQuestion++;
                loadQuestion();
            }, 1500);
        }

        function showResult() {
            gameScreen.classList.add('hide');
            resultScreen.classList.remove('hide');
            finalScoreEl.textContent = score;
            finalTotalEl.textContent = quizData.length;
        }

        function copyLink() {
            const copyText = document.getElementById("share-link");
            copyText.select();
            copyText.setSelectionRange(0, 99999);
            navigator.clipboard.writeText(copyText.value);
            alert("Ссылка скопирована: " + copyText.value);
        }
    </script>
</body>
</html>
