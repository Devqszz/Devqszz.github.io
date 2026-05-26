<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>Курсы валют НБРБ - Telegram Bot</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            min-height: 100vh;
            padding: 20px;
            position: relative;
        }
        
        body::before {
            content: '';
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" opacity="0.05"><path fill="white" d="M20,20 L30,20 L25,30 Z M70,70 L80,70 L75,80 Z M50,50 L60,50 L55,60 Z"/></svg>') repeat;
            pointer-events: none;
        }
        
        .container {
            max-width: 650px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.98);
            border-radius: 30px;
            backdrop-filter: blur(10px);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
            overflow: hidden;
            position: relative;
            z-index: 1;
        }
        
        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 25px 20px;
            text-align: center;
            position: relative;
        }
        
        .header h1 {
            color: white;
            font-size: 28px;
            margin-bottom: 8px;
            font-weight: 700;
            letter-spacing: -0.5px;
        }
        
        .header p {
            color: rgba(255, 255, 255, 0.9);
            font-size: 14px;
        }
        
        .date-section {
            background: white;
            padding: 15px 20px;
            border-bottom: 1px solid #e5e7eb;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
        }
        
        .date-box {
            display: flex;
            align-items: center;
            gap: 10px;
            background: #f3f4f6;
            padding: 8px 15px;
            border-radius: 12px;
        }
        
        .date-icon {
            font-size: 20px;
        }
        
        .date-text {
            font-size: 14px;
            color: #374151;
            font-weight: 500;
        }
        
        .rate-date {
            color: #6b7280;
            font-size: 13px;
        }
        
        .stats {
            display: flex;
            justify-content: space-around;
            background: #f9fafb;
            padding: 15px 20px;
            border-bottom: 1px solid #e5e7eb;
        }
        
        .stat-card {
            text-align: center;
            flex: 1;
        }
        
        .stat-value {
            font-size: 24px;
            font-weight: bold;
            color: #667eea;
        }
        
        .stat-label {
            font-size: 11px;
            color: #6b7280;
            margin-top: 5px;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }
        
        .currency-list {
            max-height: 500px;
            overflow-y: auto;
        }
        
        .currency-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 16px 20px;
            border-bottom: 1px solid #f0f0f0;
            transition: all 0.3s ease;
            cursor: pointer;
        }
        
        .currency-item:hover {
            background: linear-gradient(90deg, #f9fafb 0%, #ffffff 100%);
            transform: translateX(5px);
        }
        
        .currency-info {
            flex: 2;
        }
        
        .currency-name {
            font-weight: 700;
            font-size: 16px;
            color: #1f2937;
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
        }
        
        .currency-code {
            background: #e5e7eb;
            padding: 2px 8px;
            border-radius: 12px;
            font-size: 10px;
            font-weight: 600;
            color: #4b5563;
        }
        
        .currency-eng {
            font-size: 11px;
            color: #9ca3af;
            font-weight: normal;
        }
        
        .currency-nominal {
            font-size: 11px;
            color: #9ca3af;
            margin-top: 5px;
        }
        
        .currency-rate {
            text-align: right;
            flex: 1;
        }
        
        .rate-value {
            font-weight: 800;
            font-size: 18px;
            color: #1f2937;
        }
        
        .rate-currency {
            font-size: 12px;
            color: #6b7280;
            margin-left: 2px;
        }
        
        .rate-change {
            font-size: 11px;
            margin-top: 5px;
            display: flex;
            align-items: center;
            justify-content: flex-end;
            gap: 4px;
        }
        
        .rate-change.positive {
            color: #10b981;
        }
        
        .rate-change.negative {
            color: #ef4444;
        }
        
        .rate-change.neutral {
            color: #6b7280;
        }
        
        .change-badge {
            padding: 2px 8px;
            border-radius: 20px;
            font-size: 10px;
            font-weight: 600;
        }
        
        .change-badge.positive {
            background: #d1fae5;
            color: #065f46;
        }
        
        .change-badge.negative {
            background: #fee2e2;
            color: #991b1b;
        }
        
        .loading {
            text-align: center;
            padding: 60px 20px;
            color: #6b7280;
        }
        
        .loading-spinner {
            width: 50px;
            height: 50px;
            border: 3px solid #e5e7eb;
            border-top-color: #667eea;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin: 0 auto 15px;
        }
        
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
        
        .error {
            text-align: center;
            padding: 40px 20px;
            background: #fef2f2;
            margin: 20px;
            border-radius: 16px;
            color: #dc2626;
        }
        
        .refresh-btn {
            display: block;
            width: calc(100% - 40px);
            margin: 20px;
            padding: 14px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 16px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .refresh-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 25px -5px rgba(102, 126, 234, 0.4);
        }
        
        .refresh-btn:active {
            transform: translateY(0);
        }
        
        .footer {
            background: #f9fafb;
            padding: 15px 20px;
            text-align: center;
            border-top: 1px solid #e5e7eb;
        }
        
        .last-update {
            font-size: 11px;
            color: #9ca3af;
        }
        
        .source {
            font-size: 10px;
            color: #d1d5db;
            margin-top: 5px;
        }
        
        /* Стили для скроллбара */
        .currency-list::-webkit-scrollbar {
            width: 6px;
        }
        
        .currency-list::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        
        .currency-list::-webkit-scrollbar-thumb {
            background: #c7d2fe;
            border-radius: 3px;
        }
        
        @media (max-width: 480px) {
            body {
                padding: 10px;
            }
            
            .container {
                border-radius: 20px;
            }
            
            .header h1 {
                font-size: 22px;
            }
            
            .currency-item {
                padding: 12px 15px;
            }
            
            .rate-value {
                font-size: 16px;
            }
            
            .stat-value {
                font-size: 18px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🇧🇾 Курсы валют</h1>
            <p>Национальный банк Республики Беларусь</p>
        </div>
        
        <div class="date-section">
            <div class="date-box">
                <span class="date-icon">📅</span>
                <span class="date-text" id="fullDate">Загрузка...</span>
            </div>
            <div class="rate-date" id="rateDate"></div>
        </div>
        
        <div class="stats" id="stats" style="display: none;">
            <div class="stat-card">
                <div class="stat-value" id="totalCurrencies">0</div>
                <div class="stat-label">Валют</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="increasedCount">0</div>
                <div class="stat-label">Выросли 📈</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="decreasedCount">0</div>
                <div class="stat-label">Упали 📉</div>
            </div>
        </div>
        
        <div id="currencyContainer" class="currency-list">
            <div class="loading">
                <div class="loading-spinner"></div>
                <div>Загрузка курсов валют...</div>
            </div>
        </div>
        
        <button class="refresh-btn" onclick="fetchCurrencies()">
            🔄 Обновить курсы
        </button>
        
        <div class="footer">
            <div class="last-update" id="lastUpdate">—</div>
            <div class="source">Данные НБРБ • Обновляется автоматически</div>
        </div>
    </div>

    <script>
        let autoRefreshInterval;
        
        // Функция для получения курсов валют с API НБРБ
        async function fetchCurrencies() {
            const container = document.getElementById('currencyContainer');
            const statsDiv = document.getElementById('stats');
            const fullDateSpan = document.getElementById('fullDate');
            const rateDateSpan = document.getElementById('rateDate');
            
            container.innerHTML = '<div class="loading"><div class="loading-spinner"></div><div>Загрузка курсов валют...</div></div>';
            statsDiv.style.display = 'none';
            
            try {
                // API Национального банка РБ
                const response = await fetch('https://api.nbrb.by/exrates/rates?periodicity=0');
                
                if (!response.ok) {
                    throw new Error('Ошибка загрузки данных');
                }
                
                const data = await response.json();
                
                // Получаем дату актуальности курсов
                const today = new Date();
                const options = { year: 'numeric', month: 'long', day: 'numeric' };
                fullDateSpan.textContent = today.toLocaleDateString('ru-RU', options);
                rateDateSpan.textContent = `Курсы на ${today.toLocaleDateString('ru-RU')}`;
                
                // Фильтруем и сортируем валюты
                let currencies = data.filter(c => 
                    ['USD', 'EUR', 'RUB', 'CNY', 'GBP', 'JPY', 'CHF', 'PLN', 'TRY', 'UAH', 'KZT', 'CZK', 'SEK', 'NOK', 'DKK'].includes(c.Cur_Abbreviation)
                );
                
                // Сохраняем предыдущие курсы для сравнения
                let previousRates = JSON.parse(localStorage.getItem('previousRates') || '{}');
                let currentRates = {};
                
                // Статистика изменений
                let increased = 0;
                let decreased = 0;
                
                let html = '';
                
                for (const currency of currencies) {
                    const code = currency.Cur_Abbreviation;
                    const rate = currency.Cur_OfficialRate;
                    const scale = currency.Cur_Scale;
                    const name = currency.Cur_Name;
                    const previousRate = previousRates[code] || rate;
                    const change = rate - previousRate;
                    const changePercent = previousRate !== rate ? ((change / previousRate) * 100).toFixed(2) : 0;
                    
                    currentRates[code] = rate;
                    
                    if (change > 0) increased++;
                    if (change < 0) decreased++;
                    
                    const changeClass = change > 0 ? 'positive' : (change < 0 ? 'negative' : 'neutral');
                    const changeSymbol = change > 0 ? '▲' : (change < 0 ? '▼' : '●');
                    const changeColor = change > 0 ? 'positive' : (change < 0 ? 'negative' : 'neutral');
                    
                    html += `
                        <div class="currency-item" onclick="showCurrencyInfo('${code}', ${rate}, ${scale}, '${name}')">
                            <div class="currency-info">
                                <div class="currency-name">
                                    ${name}
                                    <span class="currency-code">${code}</span>
                                    <span class="currency-eng">${currency.Cur_Code}</span>
                                </div>
                                <div class="currency-nominal">Номинал: ${scale} ${code}</div>
                            </div>
                            <div class="currency-rate">
                                <div class="rate-value">
                                    ${rate.toFixed(4)} <span class="rate-currency">BYN</span>
                                </div>
                                ${previousRate !== rate ? `
                                    <div class="rate-change ${changeColor}">
                                        ${changeSymbol} ${Math.abs(change).toFixed(4)} BYN (${changePercent > 0 ? '+' : ''}${changePercent}%)
                                    </div>
                                ` : '<div class="rate-change neutral">● без изменений</div>'}
                            </div>
                        </div>
                    `;
                }
                
                // Обновляем статистику
                document.getElementById('totalCurrencies').textContent = currencies.length;
                document.getElementById('increasedCount').textContent = increased;
                document.getElementById('decreasedCount').textContent = decreased;
                statsDiv.style.display = 'flex';
                
                container.innerHTML = html;
                
                // Сохраняем текущие курсы для будущего сравнения
                localStorage.setItem('previousRates', JSON.stringify(currentRates));
                
                // Обновляем время последнего обновления
                const now = new Date();
                const timeOptions = { hour: '2-digit', minute: '2-digit', second: '2-digit' };
                document.getElementById('lastUpdate').textContent = `Последнее обновление: ${now.toLocaleTimeString('ru-RU', timeOptions)}`;
                
                // Отправляем данные в Telegram (если это Web App)
                if (window.Telegram && window.Telegram.WebApp) {
                    const tg = window.Telegram.WebApp;
                    
                    const currencyData = {
                        action: 'currency_update',
                        timestamp: new Date().toISOString(),
                        rates: currentRates,
                        statistics: {
                            total: currencies.length,
                            increased: increased,
                            decreased: decreased
                        }
                    };
                    
                    tg.sendData(JSON.stringify(currencyData));
                }
                
            } catch (error) {
                console.error('Ошибка:', error);
                container.innerHTML = `
                    <div class="error">
                        <strong>❌ Ошибка загрузки</strong><br>
                        Не удалось загрузить курсы валют<br>
                        Проверьте подключение к интернету
                    </div>
                `;
            }
        }
        
        // Функция для показа подробной информации о валюте
        function showCurrencyInfo(code, rate, scale, name) {
            if (window.Telegram && window.Telegram.WebApp) {
                window.Telegram.WebApp.showAlert(
                    `${name} (${code})\nНоминал: ${scale} ${code}\nКурс: ${rate.toFixed(4)} BYN`
                );
            } else {
                alert(`${name} (${code})\nНоминал: ${scale} ${code}\nКурс: ${rate.toFixed(4)} BYN`);
            }
        }
        
        // Функция для получения курса конкретной валюты
        async function getSpecificCurrency(currencyCode) {
            try {
                const response = await fetch(`https://api.nbrb.by/exrates/rates/${currencyCode}?parammode=2`);
                const data = await response.json();
                return {
                    name: data.Cur_Name,
                    rate: data.Cur_OfficialRate,
                    scale: data.Cur_Scale,
                    date: data.Date
                };
            } catch (error) {
                console.error('Ошибка:', error);
                return null;
            }
        }
        
        // Автоматическое обновление каждые 30 минут
        function startAutoRefresh() {
            if (autoRefreshInterval) {
                clearInterval(autoRefreshInterval);
            }
            autoRefreshInterval = setInterval(fetchCurrencies, 30 * 60 * 1000);
        }
        
        // Инициализация Telegram Web App
        if (window.Telegram && window.Telegram.WebApp) {
            const tg = window.Telegram.WebApp;
            tg.expand();
            
            tg.MainButton.setText("Обновить курсы");
            tg.MainButton.onClick(() => {
                fetchCurrencies();
                tg.showAlert("Курсы обновлены!");
            });
            tg.MainButton.show();
            
            tg.ready();
        }
        
        // Загружаем курсы при открытии страницы
        fetchCurrencies();
        startAutoRefresh();
    </script>
</body>
</html>
