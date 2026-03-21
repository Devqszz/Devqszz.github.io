# Devqszz.github.io
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>Курсы валют - Telegram Bot</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            padding: 20px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.1);
        }
        
        h1 {
            color: #667eea;
            font-size: 24px;
            margin-bottom: 10px;
            text-align: center;
        }
        
        .date {
            text-align: center;
            color: #666;
            font-size: 14px;
            margin-bottom: 20px;
            padding-bottom: 10px;
            border-bottom: 1px solid #eee;
        }
        
        .currency-list {
            margin-bottom: 20px;
        }
        
        .currency-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px;
            border-bottom: 1px solid #f0f0f0;
            transition: background 0.2s;
        }
        
        .currency-item:hover {
            background: #f8f9fa;
        }
        
        .currency-info {
            flex: 2;
        }
        
        .currency-name {
            font-weight: bold;
            font-size: 16px;
            color: #333;
        }
        
        .currency-code {
            font-size: 12px;
            color: #999;
            margin-left: 8px;
        }
        
        .currency-nominal {
            font-size: 11px;
            color: #999;
            margin-top: 4px;
        }
        
        .currency-rate {
            text-align: right;
            flex: 1;
        }
        
        .rate-value {
            font-weight: bold;
            font-size: 18px;
            color: #667eea;
        }
        
        .rate-change {
            font-size: 12px;
            margin-top: 4px;
        }
        
        .rate-change.positive {
            color: #4caf50;
        }
        
        .rate-change.negative {
            color: #f44336;
        }
        
        .rate-change.neutral {
            color: #999;
        }
        
        .loading {
            text-align: center;
            padding: 40px;
            color: #666;
        }
        
        .error {
            text-align: center;
            padding: 40px;
            color: #f44336;
            background: #ffebee;
            border-radius: 10px;
            margin: 20px;
        }
        
        .refresh-btn {
            display: block;
            width: 100%;
            padding: 12px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            cursor: pointer;
            margin-top: 20px;
            transition: transform 0.2s;
        }
        
        .refresh-btn:hover {
            transform: translateY(-2px);
        }
        
        .refresh-btn:active {
            transform: translateY(0);
        }
        
        .last-update {
            text-align: center;
            font-size: 11px;
            color: #999;
            margin-top: 15px;
            padding-top: 10px;
            border-top: 1px solid #eee;
        }
        
        @media (max-width: 480px) {
            .container {
                padding: 15px;
            }
            
            .currency-item {
                padding: 12px;
            }
            
            .rate-value {
                font-size: 16px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Курсы валют</h1>
        <div class="date" id="dateDisplay">Загрузка...</div>
        
        <div id="currencyContainer" class="currency-list">
            <div class="loading">Загрузка курсов валют...</div>
        </div>
        
        <button class="refresh-btn" onclick="fetchCurrencies()">Обновить курсы</button>
        <div class="last-update" id="lastUpdate">—</div>
    </div>

    <script>
        // Функция для получения курсов валют с API Центробанка РФ
        async function fetchCurrencies() {
            const container = document.getElementById('currencyContainer');
            const dateDisplay = document.getElementById('dateDisplay');
            const lastUpdateSpan = document.getElementById('lastUpdate');
            
            container.innerHTML = '<div class="loading">Загрузка курсов валют...</div>';
            
            try {
                // Используем API Центробанка РФ (бесплатный, без ключа)
                const response = await fetch('https://www.cbr-xml-daily.ru/daily_json.js');
                
                if (!response.ok) {
                    throw new Error('Ошибка загрузки данных');
                }
                
                const data = await response.json();
                
                // Обновляем дату
                const date = new Date(data.Date);
                dateDisplay.textContent = `Курсы на ${date.toLocaleDateString('ru-RU')}`;
                
                // Выбираем основные валюты для отображения
                const currenciesToShow = ['USD', 'EUR', 'CNY', 'GBP', 'JPY', 'CHF', 'TRY', 'KZT', 'UAH', 'BYN'];
                
                let html = '';
                
                for (const code of currenciesToShow) {
                    if (data.Valute[code]) {
                        const currency = data.Valute[code];
                        const rate = currency.Value;
                        const previousRate = currency.Previous;
                        const change = rate - previousRate;
                        const changePercent = (change / previousRate * 100).toFixed(2);
                        const changeClass = change > 0 ? 'positive' : (change < 0 ? 'negative' : 'neutral');
                        const changeSymbol = change > 0 ? '▲' : (change < 0 ? '▼' : '●');
                        
                        html += `
                            <div class="currency-item">
                                <div class="currency-info">
                                    <div class="currency-name">
                                        ${currency.Name}
                                        <span class="currency-code">${currency.CharCode}</span>
                                    </div>
                                    <div class="currency-nominal">${currency.Nominal} ${currency.CharCode}</div>
                                </div>
                                <div class="currency-rate">
                                    <div class="rate-value">${rate.toFixed(2)} ₽</div>
                                    <div class="rate-change ${changeClass}">
                                        ${changeSymbol} ${Math.abs(change).toFixed(2)} ₽ (${changePercent}%)
                                    </div>
                                </div>
                            </div>
                        `;
                    }
                }
                
                // Добавляем курс Bitcoin если есть (не всегда доступен)
                if (data.Valute['BTC']) {
                    const btc = data.Valute['BTC'];
                    html += `
                        <div class="currency-item">
                            <div class="currency-info">
                                <div class="currency-name">
                                    Bitcoin
                                    <span class="currency-code">BTC</span>
                                </div>
                            </div>
                            <div class="currency-rate">
                                <div class="rate-value">${btc.Value.toFixed(2)} ₽</div>
                            </div>
                        </div>
                    `;
                }
                
                container.innerHTML = html;
                
                // Обновляем время последнего обновления
                const now = new Date();
                lastUpdateSpan.textContent = `Последнее обновление: ${now.toLocaleTimeString('ru-RU')}`;
                
                // Отправляем данные в Telegram (если это Web App)
                if (window.Telegram && window.Telegram.WebApp) {
                    const tg = window.Telegram.WebApp;
                    
                    // Формируем данные для отправки боту
                    const currencyData = {
                        action: 'currency_update',
                        timestamp: new Date().toISOString(),
                        rates: {}
                    };
                    
                    for (const code of currenciesToShow) {
                        if (data.Valute[code]) {
                            currencyData.rates[code] = {
                                value: data.Valute[code].Value,
                                nominal: data.Valute[code].Nominal
                            };
                        }
                    }
                    
                    tg.sendData(JSON.stringify(currencyData));
                }
                
            } catch (error) {
                console.error('Ошибка:', error);
                container.innerHTML = `
                    <div class="error">
                        ❌ Не удалось загрузить курсы валют<br>
                        Проверьте подключение к интернету
                    </div>
                `;
            }
        }
        
        // Функция для получения курса конкретной валюты
        async function getCurrencyRate(currencyCode) {
            try {
                const response = await fetch('https://www.cbr-xml-daily.ru/daily_json.js');
                const data = await response.json();
                
                if (data.Valute[currencyCode]) {
                    return {
                        name: data.Valute[currencyCode].Name,
                        rate: data.Valute[currencyCode].Value,
                        nominal: data.Valute[currencyCode].Nominal,
                        previous: data.Valute[currencyCode].Previous
                    };
                }
                return null;
            } catch (error) {
                console.error('Ошибка:', error);
                return null;
            }
        }
        
        // Автоматическое обновление каждые 10 минут
        let autoRefreshInterval;
        
        function startAutoRefresh() {
            if (autoRefreshInterval) {
                clearInterval(autoRefreshInterval);
            }
            autoRefreshInterval = setInterval(fetchCurrencies, 10 * 60 * 1000); // 10 минут
        }
        
        // Инициализация Telegram Web App
        if (window.Telegram && window.Telegram.WebApp) {
            const tg = window.Telegram.WebApp;
            tg.expand(); // Расширяем на весь экран
            
            // Показываем основную кнопку
            tg.MainButton.setText("Обновить");
            tg.MainButton.onClick(() => {
                fetchCurrencies();
            });
            tg.MainButton.show();
            
            // Уведомляем, что приложение готово
            tg.ready();
        }
        
        // Загружаем курсы при открытии страницы
        fetchCurrencies();
        startAutoRefresh();
    </script>
</body>
</html>
