# Devqszz.github.io
<!DOCTYPE HTML>
<html lang="ru">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Web App for Telegram Bot">
    <title>Telegram Bot Web App</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
            color: #333;
        }
        
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.1);
        }
        
        h1 {
            color: #667eea;
            margin-bottom: 20px;
            font-size: 28px;
            text-align: center;
        }
        
        .content {
            margin: 20px 0;
            line-height: 1.6;
        }
        
        p {
            margin-bottom: 15px;
            color: #555;
        }
        
        .info-box {
            background: #f8f9fa;
            border-left: 4px solid #667eea;
            padding: 15px;
            margin: 20px 0;
            border-radius: 8px;
        }
        
        button {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
            margin: 5px;
        }
        
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
        
        .user-info {
            background: #e3f2fd;
            padding: 15px;
            border-radius: 10px;
            margin: 15px 0;
        }
        
        .user-info h3 {
            color: #1976d2;
            margin-bottom: 10px;
        }
        
        .stats {
            display: flex;
            justify-content: space-around;
            margin: 20px 0;
            text-align: center;
        }
        
        .stat-card {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 10px;
            flex: 1;
            margin: 0 5px;
        }
        
        .stat-number {
            font-size: 24px;
            font-weight: bold;
            color: #667eea;
        }
        
        .stat-label {
            font-size: 12px;
            color: #777;
            margin-top: 5px;
        }
        
        input {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
        }
        
        @media (max-width: 480px) {
            .container {
                padding: 20px;
            }
            
            h1 {
                font-size: 24px;
            }
            
            .stats {
                flex-direction: column;
            }
            
            .stat-card {
                margin: 5px 0;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🌟 Добро пожаловать!</h1>
        
        <div class="content">
            <p>Это веб-приложение для Telegram бота. Здесь вы можете взаимодействовать с дополнительным функционалом.</p>
            
            <div class="info-box">
                <strong>📱 Как это работает:</strong><br>
                Это мини-приложение Telegram, которое открывается прямо в чате. Вы можете взаимодействовать с ним, не покидая мессенджер.
            </div>
            
            <div class="user-info" id="userInfo">
                <h3>👤 Информация о пользователе</h3>
                <p>Загрузка данных...</p>
            </div>
            
            <div class="stats">
                <div class="stat-card">
                    <div class="stat-number" id="visitCount">0</div>
                    <div class="stat-label">Посещений</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number" id="clickCount">0</div>
                    <div class="stat-label">Кликов</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number" id="timeSpent">0</div>
                    <div class="stat-label">Секунд на странице</div>
                </div>
            </div>
            
            <div style="text-align: center; margin: 20px 0;">
                <button onclick="handleButtonClick()">🎯 Нажми меня!</button>
                <button onclick="sendDataToBot()">📤 Отправить данные боту</button>
            </div>
            
            <div>
                <input type="text" id="userInput" placeholder="Введите ваше сообщение...">
                <button onclick="sendMessage()">💬 Отправить</button>
            </div>
        </div>
    </div>
    
    <script>
        // Инициализация Telegram Web App
        const tg = window.Telegram.WebApp;
        
        // Расширяем на весь экран
        tg.expand();
        
        // Основная кнопка
        tg.MainButton.setText("Закрыть");
        tg.MainButton.onClick(() => {
            tg.close();
        });
        tg.MainButton.show();
        
        // Счетчики
        let visitCount = localStorage.getItem('visitCount') || 0;
        let clickCount = 0;
        let startTime = Date.now();
        
        // Обновляем счетчик посещений
        visitCount = parseInt(visitCount) + 1;
        localStorage.setItem('visitCount', visitCount);
        document.getElementById('visitCount').textContent = visitCount;
        
        // Получаем данные пользователя из Telegram
        const user = tg.initDataUnsafe?.user;
        if (user) {
            const userInfoHtml = `
                <h3>👤 Информация о пользователе</h3>
                <p><strong>Имя:</strong> ${user.first_name || 'Не указано'} ${user.last_name || ''}</p>
                <p><strong>Username:</strong> ${user.username || 'Не указан'}</p>
                <p><strong>ID:</strong> ${user.id || 'Неизвестен'}</p>
                <p><strong>Язык:</strong> ${user.language_code || 'Не указан'}</p>
            `;
            document.getElementById('userInfo').innerHTML = userInfoHtml;
        }
        
        // Функция для кнопки
        function handleButtonClick() {
            clickCount++;
            document.getElementById('clickCount').textContent = clickCount;
            tg.showPopup({
                title: 'Уведомление',
                message: `Вы нажали на кнопку ${clickCount} раз(а)!`,
                buttons: [{type: 'ok'}]
            });
        }
        
        // Отправка данных боту
        function sendDataToBot() {
            const data = {
                action: 'user_action',
                clicks: clickCount,
                visits: visitCount,
                timestamp: new Date().toISOString()
            };
            tg.sendData(JSON.stringify(data));
            tg.showAlert('Данные отправлены боту!');
        }
        
        // Отправка сообщения
        function sendMessage() {
            const input = document.getElementById('userInput');
            const message = input.value.trim();
            if (message) {
                tg.sendData(JSON.stringify({
                    action: 'message',
                    text: message
                }));
                input.value = '';
                tg.showAlert('Сообщение отправлено!');
            }
        }
        
        // Обновляем время на странице
        setInterval(() => {
            const seconds = Math.floor((Date.now() - startTime) / 1000);
            document.getElementById('timeSpent').textContent = seconds;
        }, 1000);
        
        // Обработка данных от бота
        tg.onEvent('mainButtonClicked', () => {
            console.log('Main button clicked');
        });
        
        // Отправляем событие загрузки
        tg.ready();
        
        console.log('Web App загружен');
    </script>
</body>
</html>
