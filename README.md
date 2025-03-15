<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Scientific Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            height: 100vh;
            background-color: #222;
            margin: 0;
            padding-top: 20px;
        }
        .calculator {
            width: 320px;
            background: #333;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
            text-align: center;
            color: white;
            position: relative;
        }
        #display {
            width: 100%;
            height: 60px;
            font-size: 1.8em;
            text-align: right;
            padding: 10px;
            border: none;
            background: black;
            color: white;
            border-radius: 5px;
        }
        .buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 8px;
            margin-top: 10px;
        }
        .scientific-buttons {
            display: none;
            grid-template-columns: repeat(4, 1fr);
            gap: 8px;
            margin-top: 10px;
        }
        button {
            height: 50px;
            font-size: 1.2em;
            border: none;
            cursor: pointer;
            border-radius: 5px;
            background-color: #444;
            color: white;
        }
        button:active {
            background-color: #666;
        }
        .operator {
            background-color: #f39c12;
        }
        .operator:active {
            background-color: #e67e22;
        }
        /* Menu */
        .menu {
            position: absolute;
            top: 10px;
            right: 10px;
            display: flex;
            gap: 10px;
        }
        .menu button {
            background: none;
            border: none;
            font-size: 24px;
            color: white;
            cursor: pointer;
        }
        .history {
            display: none;
            position: absolute;
            top: 40px;
            right: 10px;
            width: 260px;
            max-height: 150px;
            overflow-y: auto;
            background: #222;
            border: 1px solid #555;
            padding: 5px;
            border-radius: 5px;
            text-align: left;
            z-index: 100;
        }
        .history div {
            padding: 5px;
            border-bottom: 1px solid #555;
            color: white;
        }
    </style>
</head>
<body>

    <div class="calculator">
        <!-- Three-dot menu & Scientific Toggle -->
        <div class="menu">
            <button onclick="toggleScientific()">𝛑</button>  <!-- Symbol for Scientific Mode -->
            <button onclick="toggleHistory()">⋮</button>
        </div>

        <!-- History Panel -->
        <div class="history" id="history">
            <h4>History</h4>
            <div id="historyContent"></div>
        </div>

        <input type="text" id="display" disabled>

        <!-- Scientific Buttons -->
        <div class="scientific-buttons" id="scientificButtons">
            <button onclick="appendValue('Math.PI')">π</button>
            <button onclick="appendValue('Math.E')">e</button>
            <button onclick="calculateFunction('sqrt')">√</button>
            <button onclick="calculateFunction('pow2')">x²</button>
            <button onclick="calculateFunction('sin')">sin</button>
            <button onclick="calculateFunction('cos')">cos</button>
            <button onclick="calculateFunction('tan')">tan</button>
            <button onclick="calculateFunction('log')">log</button>
            <button onclick="calculateFunction('ln')">ln</button>
            <button onclick="calculateFunction('fact')">x!</button>
            <button onclick="appendValue('(')">(</button>
            <button onclick="appendValue(')')">)</button>
        </div>

        <div class="buttons">
            <button onclick="clearDisplay()">C</button>
            <button onclick="backspace()">⌫</button>
            <button onclick="appendValue('%')" class="operator">%</button>
            <button onclick="appendValue('/')" class="operator">÷</button>
            <button onclick="appendValue('7')">7</button>
            <button onclick="appendValue('8')">8</button>
            <button onclick="appendValue('9')">9</button>
            <button onclick="appendValue('*')" class="operator">×</button>
            <button onclick="appendValue('4')">4</button>
            <button onclick="appendValue('5')">5</button>
            <button onclick="appendValue('6')">6</button>
            <button onclick="appendValue('-')" class="operator">-</button>
            <button onclick="appendValue('1')">1</button>
            <button onclick="appendValue('2')">2</button>
            <button onclick="appendValue('3')">3</button>
            <button onclick="appendValue('+')" class="operator">+</button>
            <button onclick="appendValue('0')" style="grid-column: span 2;">0</button>
            <button onclick="appendValue('.')">.</button>
            <button onclick="calculateResult()" class="operator">=</button>
        </div>
    </div>

    <script>
        let history = [];
        let historyVisible = false;
        let scientificVisible = false;

        function appendValue(value) {
            document.getElementById("display").value += value;
            hideHistory();  // Hide history when starting a new operation
        }

        function clearDisplay() {
            document.getElementById("display").value = "";
            hideHistory();
        }

        function backspace() {
            let display = document.getElementById("display");
            display.value = display.value.slice(0, -1);
            hideHistory();
        }

        function calculateResult() {
            try {
                let expression = document.getElementById("display").value;
                let result = eval(expression);
                document.getElementById("display").value = result;
                
                history.push(expression + " = " + result);
                updateHistory();
            } catch {
                alert("Invalid expression");
            }
            hideHistory();
        }

        function calculateFunction(type) {
            let display = document.getElementById("display");
            let value = parseFloat(display.value);
            if (isNaN(value)) return;

            let result;
            switch (type) {
                case 'sqrt': result = Math.sqrt(value); break;
                case 'pow2': result = Math.pow(value, 2); break;
                case 'sin': result = Math.sin(value * Math.PI / 180); break;
                case 'cos': result = Math.cos(value * Math.PI / 180); break;
                case 'tan': result = Math.tan(value * Math.PI / 180); break;
                case 'log': result = Math.log10(value); break;
                case 'ln': result = Math.log(value); break;
                case 'fact': result = factorial(value); break;
            }
            display.value = result;
        }

        function factorial(n) {
            return n === 0 ? 1 : n * factorial(n - 1);
        }

        function toggleHistory() {
            historyVisible = !historyVisible;
            document.getElementById("history").style.display = historyVisible ? "block" : "none";
        }

        function toggleScientific() {
            scientificVisible = !scientificVisible;
            document.getElementById("scientificButtons").style.display = scientificVisible ? "grid" : "none";
        }

        function hideHistory() {
            document.getElementById("history").style.display = "none";
            historyVisible = false;
        }
    </script>
</body>
</html>
