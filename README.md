<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Currency Converter</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .converter-container {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 450px;
            transition: transform 0.3s ease;
        }

        .converter-container:hover {
            transform: translateY(-5px);
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 30px;
            font-size: 28px;
            font-weight: 600;
        }

        .input-group {
            margin-bottom: 25px;
            position: relative;
        }

        label {
            display: block;
            margin-bottom: 8px;
            color: #555;
            font-weight: 500;
            font-size: 14px;
        }

        input, select {
            width: 100%;
            padding: 15px;
            border: 2px solid #e0e0e0;
            border-radius: 12px;
            font-size: 16px;
            transition: all 0.3s ease;
            background: white;
        }

        input:focus, select:focus {
            outline: none;
            border-color: #667eea;
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.2);
        }

        .currency-row {
            display: flex;
            gap: 15px;
            align-items: end;
        }

        .amount-input {
            flex: 2;
        }

        .currency-select {
            flex: 1;
        }

        .swap-button {
            background: #667eea;
            color: white;
            border: none;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 18px;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 15px auto;
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
        }

        .swap-button:hover {
            background: #5a6fd8;
            transform: rotate(180deg) scale(1.1);
        }

        .result {
            background: linear-gradient(135deg, #667eea, #764ba2);
            color: white;
            padding: 20px;
            border-radius: 15px;
            text-align: center;
            font-size: 18px;
            font-weight: 600;
            margin-top: 25px;
            opacity: 0;
            transform: translateY(20px);
            transition: all 0.5s ease;
        }

        .result.show {
            opacity: 1;
            transform: translateY(0);
        }

        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border-top-color: white;
            animation: spin 1s ease-in-out infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        .rate-info {
            text-align: center;
            color: #666;
            font-size: 12px;
            margin-top: 20px;
            opacity: 0.8;
        }

        @media (max-width: 480px) {
            .converter-container {
                padding: 30px 20px;
            }
            
            .currency-row {
                flex-direction: column;
                gap: 15px;
            }
            
            h1 {
                font-size: 24px;
            }
        }
    </style>
</head>
<body>
    <div class="converter-container">
        <h1>Currency Converter</h1>
        
        <div class="input-group">
            <label for="fromAmount">Amount</label>
            <div class="currency-row">
                <div class="amount-input">
                    <input type="number" id="fromAmount" placeholder="Enter amount" value="1" min="0" step="0.01">
                </div>
                <div class="currency-select">
                    <select id="fromCurrency">
                        <option value="USD">USD</option>
                        <option value="EUR">EUR</option>
                        <option value="GBP">GBP</option>
                        <option value="JPY">JPY</option>
                        <option value="CAD">CAD</option>
                        <option value="AUD">AUD</option>
                        <option value="CHF">CHF</option>
                        <option value="CNY">CNY</option>
                        <option value="INR">INR</option>
                        <option value="KRW">KRW</option>
                    </select>
                </div>
            </div>
        </div>

        <button class="swap-button" onclick="swapCurrencies()">⇅</button>

        <div class="input-group">
            <label for="toAmount">Converted Amount</label>
            <div class="currency-row">
                <div class="amount-input">
                    <input type="number" id="toAmount" placeholder="Converted amount" readonly>
                </div>
                <div class="currency-select">
                    <select id="toCurrency">
                        <option value="USD">USD</option>
                        <option value="EUR" selected>EUR</option>
                        <option value="GBP">GBP</option>
                        <option value="JPY">JPY</option>
                        <option value="CAD">CAD</option>
                        <option value="AUD">AUD</option>
                        <option value="CHF">CHF</option>
                        <option value="CNY">CNY</option>
                        <option value="INR">INR</option>
                        <option value="KRW">KRW</option>
                    </select>
                </div>
            </div>
        </div>

        <div class="result" id="result"></div>
        
        <div class="rate-info">
            Exchange rates are simulated for demonstration purposes
        </div>
    </div>

    <script>
        // Simulated exchange rates (in a real app, you'd fetch from an API)
        const exchangeRates = {
            USD: { EUR: 0.85, GBP: 0.73, JPY: 110, CAD: 1.25, AUD: 1.35, CHF: 0.92, CNY: 6.45, INR: 74.5, KRW: 1180 },
            EUR: { USD: 1.18, GBP: 0.86, JPY: 129, CAD: 1.47, AUD: 1.59, CHF: 1.08, CNY: 7.6, INR: 87.8, KRW: 1390 },
            GBP: { USD: 1.37, EUR: 1.16, JPY: 150, CAD: 1.71, AUD: 1.85, CHF: 1.26, CNY: 8.84, INR: 102, KRW: 1615 },
            JPY: { USD: 0.009, EUR: 0.0078, GBP: 0.0067, CAD: 0.011, AUD: 0.012, CHF: 0.0084, CNY: 0.059, INR: 0.68, KRW: 10.7 },
            CAD: { USD: 0.8, EUR: 0.68, GBP: 0.58, JPY: 88, AUD: 1.08, CHF: 0.74, CNY: 5.16, INR: 59.6, KRW: 944 },
            AUD: { USD: 0.74, EUR: 0.63, GBP: 0.54, JPY: 81.5, CAD: 0.93, CHF: 0.68, CNY: 4.78, INR: 55.2, KRW: 874 },
            CHF: { USD: 1.09, EUR: 0.93, GBP: 0.79, JPY: 119, CAD: 1.36, AUD: 1.47, CNY: 7.03, INR: 81.2, KRW: 1285 },
            CNY: { USD: 0.155, EUR: 0.132, GBP: 0.113, JPY: 17.1, CAD: 0.194, AUD: 0.209, CHF: 0.142, INR: 11.55, KRW: 183 },
            INR: { USD: 0.0134, EUR: 0.0114, GBP: 0.0098, JPY: 1.47, CAD: 0.0168, AUD: 0.0181, CHF: 0.0123, CNY: 0.0866, KRW: 15.84 },
            KRW: { USD: 0.00085, EUR: 0.00072, GBP: 0.00062, JPY: 0.093, CAD: 0.00106, AUD: 0.00114, CHF: 0.00078, CNY: 0.00547, INR: 0.063 }
        };

        function convertCurrency() {
            const amount = parseFloat(document.getElementById('fromAmount').value);
            const fromCurrency = document.getElementById('fromCurrency').value;
            const toCurrency = document.getElementById('toCurrency').value;
            const resultDiv = document.getElementById('result');
            const toAmountInput = document.getElementById('toAmount');

            if (!amount || amount <= 0) {
                resultDiv.classList.remove('show');
                toAmountInput.value = '';
                return;
            }

            // Show loading
            resultDiv.innerHTML = '<span class="loading"></span> Converting...';
            resultDiv.classList.add('show');

            // Simulate API delay
            setTimeout(() => {
                let convertedAmount;
                
                if (fromCurrency === toCurrency) {
                    convertedAmount = amount;
                } else {
                    const rate = exchangeRates[fromCurrency][toCurrency];
                    convertedAmount = amount * rate;
                }

                const formattedAmount = convertedAmount.toLocaleString('en-US', {
                    minimumFractionDigits: 2,
                    maximumFractionDigits: 2
                });

                toAmountInput.value = convertedAmount.toFixed(2);
                resultDiv.innerHTML = `${amount} ${fromCurrency} = ${formattedAmount} ${toCurrency}`;
            }, 500);
        }

        function swapCurrencies() {
            const fromCurrency = document.getElementById('fromCurrency');
            const toCurrency = document.getElementById('toCurrency');
            const fromAmount = document.getElementById('fromAmount');
            const toAmount = document.getElementById('toAmount');

            // Swap currency selections
            const tempCurrency = fromCurrency.value;
            fromCurrency.value = toCurrency.value;
            toCurrency.value = tempCurrency;

            // Swap amounts
            const tempAmount = fromAmount.value;
            fromAmount.value = toAmount.value || tempAmount;

            // Convert with new values
            convertCurrency();
        }

        // Event listeners
        document.getElementById('fromAmount').addEventListener('input', convertCurrency);
        document.getElementById('fromCurrency').addEventListener('change', convertCurrency);
        document.getElementById('toCurrency').addEventListener('change', convertCurrency);

        // Initial conversion
        convertCurrency();
    </script>
</body>
</html>
