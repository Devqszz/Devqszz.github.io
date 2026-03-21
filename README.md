# Devqszz.github.io
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>Telegram Web App</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            color: white;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: rgba(255,255,255,0.95);
            border-radius: 20px;
            padding: 30px;
            color: #333;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
        }
        h1 {
            color: #667eea;
            margin-bottom: 20px;
        }
        button {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            margin: 10px 5px;
        }
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
        .info {
            background: #f0f0f0;
            padding: 15px;
            border-radius: 10px;
            margin: 20px 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🌟 Web App работает!</h1>
        <p>Если вы видите этот текст, значит Web App успешно загрузился!</p>
        
        <div class="info" id="userInfo">
            <strong>📱 Информация о пользователе:</strong><br>
            Загрузка...
        </div>
        
        <button onclick="testClick()">Нажми меня!</button>
        <button onclick="closeApp()">Закрыть</button>
    </div>

    <!-- Подключаем Telegram Web App SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    
    <script>
        // Ждем загрузки страницы
        document.addEventListener('DOMContentLoaded', function() {
            console.log("Страница загружена");
            
            // Проверяем наличие Telegram Web App
            if (window.Telegram && window.Telegram.WebApp) {
                const tg = window.Telegram.WebApp;
                
                // Расширяем на весь экран
                tg.expand();
                
                // Показываем основную кнопку
                tg.MainButton.setText("Закрыть");
                tg.MainButton.onClick(() => {
                    tg.close();
                });
                tg.MainButton.show();
                
                // Получаем данные пользователя
                const user = tg.initDataUnsafe?.user;
                if (user) {
                    document.getElementById('userInfo').innerHTML = `
                        <strong>📱 Информация о пользователе:</strong><br>
                        👤 Имя: ${user.first_name || 'Не указано'} ${user.last_name || ''}<br>
                        🆔 ID: ${user.id || 'Неизвестен'}<br>
                        📧 Username: ${user.username || 'Не указан'}<br>
                        🌐 Язык: ${user.language_code || 'Не указан'}
                    `;
                } else {
                    document.getElementById('userInfo').innerHTML += "<br>⚠️ Данные пользователя не получены";
                }
                
                // Уведомляем Telegram, что приложение готово
                tg.ready();
                
                console.log("Telegram Web App инициализирован");
            } else {
                document.getElementById('userInfo').innerHTML += "<br>❌ Telegram Web App SDK не загружен!";
                console.error("Telegram Web App не найден");
            }
        });
        
        function testClick() {
            if (window.Telegram && window.Telegram.WebApp) {
                window.Telegram.WebApp.showAlert("Кнопка нажата! 🎉");
            } else {
                alert("Кнопка нажата! (Telegram Web App не доступен)");
            }
        }
        
        function closeApp() {
            if (window.Telegram && window.Telegram.WebApp) {
                window.Telegram.WebApp.close();
            }
        }
    </script>
</body>
</html>
