# Password-Generator by dev_wisz

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Password Generator</title>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Password Generator</h1>
        </div>

        <div class="password-display">
            <input type="text" id="password" readonly>
            <button class="copy-btn" id="copyBtn">📋</button>
        </div>

        <div class="options">
            <div class="option-group">
                <div class="option-header">Include Characters:</div>
                <div class="checkbox-group">
                    <label class="checkbox-container">
                        <input type="checkbox" id="uppercase" checked>
                        Uppercase
                    </label>
                    <label class="checkbox-container">
                        <input type="checkbox" id="lowercase" checked>
                        Lowercase
                    </label>
                    <label class="checkbox-container">
                        <input type="checkbox" id="numbers" checked>
                        Numbers
                    </label>
                    <label class="checkbox-container">
                        <input type="checkbox" id="symbols" checked>
                        Symbols
                    </label>
                </div>
            </div>

            <div class="slider-container">
                <div class="slider-header">
                    <span>Password Length</span>
                    <span id="lengthValue">16</span>
                </div>
                <input type="range" id="length" class="slider" min="8" max="32" value="16">
            </div>
        </div>

        <button class="generate-btn" id="generateBtn">Generate Password</button>

        <div class="history">
            <div class="history-header">
                <span>Recent Passwords</span>
                <button class="clear-btn" id="clearBtn">Clear History</button>
            </div>
            <div id="passwordHistory"></div>
        </div>
    </div>

    <div class="toast" id="toast"></div>

    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        }

        body {
            background: #1a1a2e;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
            color: #fff;
        }

        .container {
            width: 100%;
            max-width: 450px;
            background: #222;
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 4px 30px rgba(0, 0, 0, 0.2);
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
        }

        h1 {
            color: #4f8cff;
            font-size: 1.8rem;
            margin-bottom: 10px;
        }

        .password-display {
            position: relative;
            background: #333;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        #password {
            width: 100%;
            background: none;
            border: none;
            color: #4f8cff;
            font-family: monospace;
            font-size: 1.2rem;
            outline: none;
            padding-right: 40px;
        }

        .copy-btn {
            position: absolute;
            right: 15px;
            top: 50%;
            transform: translateY(-50%);
            background: none;
            border: none;
            color: #4f8cff;
            cursor: pointer;
            font-size: 1.2rem;
            padding: 5px;
        }

        .options {
            background: #333;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        .option-group {
            margin-bottom: 15px;
        }

        .option-header {
            color: #4f8cff;
            margin-bottom: 10px;
            font-weight: 500;
        }

        .checkbox-group {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
            gap: 10px;
        }

        .checkbox-container {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        input[type="checkbox"] {
            width: 16px;
            height: 16px;
            cursor: pointer;
        }

        .slider-container {
            margin-top: 20px;
        }

        .slider-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }

        .slider {
            width: 100%;
            height: 5px;
            background: #444;
            border-radius: 5px;
            outline: none;
            transition: 0.2s;
            -webkit-appearance: none;
        }

        .slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 20px;
            height: 20px;
            background: #4f8cff;
            border-radius: 50%;
            cursor: pointer;
        }

        .generate-btn {
            width: 100%;
            padding: 15px;
            border: none;
            border-radius: 10px;
            background: #4f8cff;
            color: #fff;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: 0.3s;
        }

        .generate-btn:hover {
            background: #4178db;
        }

        .history {
            background: #333;
            padding: 20px;
            border-radius: 10px;
            margin-top: 20px;
            max-height: 200px;
            overflow-y: auto;
        }

        .history-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .clear-btn {
            background: none;
            border: none;
            color: #ff4757;
            cursor: pointer;
            font-size: 0.9rem;
        }

        .history-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            background: #444;
            border-radius: 5px;
            margin-bottom: 8px;
        }

        .toast {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: #4f8cff;
            color: #fff;
            padding: 12px 24px;
            border-radius: 8px;
            transform: translateY(100px);
            transition: 0.3s;
        }

        .toast.show {
            transform: translateY(0);
        }

        @media (max-width: 480px) {
            .container {
                padding: 20px;
            }

            h1 {
                font-size: 1.5rem;
            }

            #password {
                font-size: 1rem;
            }

            .checkbox-group {
                grid-template-columns: 1fr;
            }
        }
    </style>

    <script>
        class PasswordGenerator {
            constructor() {
                this.chars = {
                    uppercase: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ',
                    lowercase: 'abcdefghijklmnopqrstuvwxyz',
                    numbers: '0123456789',
                    symbols: '!@#$%^&*()_+-=[]{}|;:,.<>?'
                };
                this.history = JSON.parse(localStorage.getItem('passwordHistory')) || [];
                this.maxHistory = 5;
                
                this.initializeEventListeners();
                this.generatePassword();
                this.updateHistoryDisplay();
            }

            initializeEventListeners() {
                document.getElementById('generateBtn').addEventListener('click', () => this.generatePassword());
                document.getElementById('copyBtn').addEventListener('click', () => this.copyPassword());
                document.getElementById('clearBtn').addEventListener('click', () => this.clearHistory());
                document.getElementById('length').addEventListener('input', (e) => {
                    document.getElementById('lengthValue').textContent = e.target.value;
                });
            }

            generatePassword() {
                let chars = '';
                if(document.getElementById('uppercase').checked) chars += this.chars.uppercase;
                if(document.getElementById('lowercase').checked) chars += this.chars.lowercase;
                if(document.getElementById('numbers').checked) chars += this.chars.numbers;
                if(document.getElementById('symbols').checked) chars += this.chars.symbols;

                if(!chars) {
                    this.showToast('Please select at least one character type');
                    return;
                }

                const length = document.getElementById('length').value;
                let password = '';

                for(let i = 0; i < length; i++) {
                    password += chars.charAt(Math.floor(Math.random() * chars.length));
                }

                document.getElementById('password').value = password;
                this.addToHistory(password);
                this.showToast('New password generated!');
            }

            copyPassword() {
                const password = document.getElementById('password').value;
                if(!password) return;

                navigator.clipboard.writeText(password)
                    .then(() => this.showToast('Password copied!'));
            }

            addToHistory(password) {
                this.history.unshift(password);
                if(this.history.length > this.maxHistory) {
                    this.history.pop();
                }
                this.updateHistoryDisplay();
                this.saveHistory();
            }

            updateHistoryDisplay() {
                const historyContainer = document.getElementById('passwordHistory');
                historyContainer.innerHTML = this.history.map(pass => `
                    <div class="history-item">
                        <span>${pass}</span>
                        <button class="copy-btn" onclick="navigator.clipboard.writeText('${pass}').then(() => passwordGenerator.showToast('Password copied!'))">📋</button>
                    </div>
                `).join('');
            }

            clearHistory() {
                this.history = [];
                this.updateHistoryDisplay();
                this.saveHistory();
                this.showToast('History cleared');
            }

            saveHistory() {
                localStorage.setItem('passwordHistory', JSON.stringify(this.history));
            }

            showToast(message) {
                const toast = document.getElementById('toast');
                toast.textContent = message;
                toast.classList.add('show');
                setTimeout(() => toast.classList.remove('show'), 2000);
            }
        }

        const passwordGenerator = new PasswordGenerator();
    </script>
</body>
</html>
