<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Calculator</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f0f0f0;
            font-family: 'Roboto', sans-serif;
        }

        .calculator {
            width: 320px;
            background-color: #393939;
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
            border-radius: 8px;
            overflow: hidden;
            perspective: 800px;
        }

        .calculator-screen {
            width: 100%;
            height: 80px;
            border: none;
            background-color: #262626;
            color: #fff;
            font-size: 2.5rem;
            text-align: right;
            padding-right: 20px;
            padding-left: 10px;
            border-radius: 5px;
            margin-bottom: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            transform: rotateX(30deg);
        }

        .calculator-keys {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            padding: 10px;
            transform-style: preserve-3d;
        }

        button {
            height: 60px;
            background-color: #505050;
            color: #fff;
            font-size: 1.5rem;
            border-radius: 5px;
            border: none;
            cursor: pointer;
            transition: transform 0.3s ease;
            transform-style: preserve-3d;
        }

        .operator {
            background-color: #ff9f00;
        }

        .equal-sign {
            background-color: #4caf50;
        }

        .all-clear {
            background-color: #f44336;
        }

        button:hover {
            transform: translateY(-5px);
        }

        button:active {
            transform: translateY(0);
        }

        @keyframes rotateY {
            from {
                transform: rotateY(0deg);
            }
            to {
                transform: rotateY(360deg);
            }
        }

        .calculator-keys button:active {
            animation: rotateY 0.5s forwards;
        }
    </style>
</head>
<body>
    <div class="calculator">
        <input type="text" class="calculator-screen" value="" disabled />
        <div class="calculator-keys">
            <button type="button" class="operator" value="+">+</button>
            <button type="button" class="operator" value="-">-</button>
            <button type="button" class="operator" value="*">&times;</button>
            <button type="button" class="operator" value="/">&divide;</button>
            <button type="button" value="7">7</button>
            <button type="button" value="8">8</button>
            <button type="button" value="9">9</button>
            <button type="button" value="4">4</button>
            <button type="button" value="5">5</button>
            <button type="button" value="6">6</button>
            <button type="button" value="1">1</button>
            <button type="button" value="2">2</button>
            <button type="button" value="3">3</button>
            <button type="button" value="0">0</button>
            <button type="button" class="decimal" value=".">.</button>
            <button type="button" class="all-clear" value="all-clear">AC</button>
            <button type="button" class="equal-sign" value="=">=</button>
        </div>
    </div>
    <script>
        const calculator = {
            displayValue: '0',
            firstOperand: null,
            waitingForSecondOperand: false,
            operator: null,
        };

        function inputDigit(digit) {
            const { displayValue, waitingForSecondOperand } = calculator;

            if (waitingForSecondOperand === true) {
                calculator.displayValue = digit;
                calculator.waitingForSecondOperand = false;
            } else {
                calculator.displayValue = displayValue === '0' ? digit : displayValue + digit;
            }
        }

        function inputDecimal(dot) {
            if (calculator.waitingForSecondOperand === true) {
                calculator.displayValue = '0.'
                calculator.waitingForSecondOperand = false;
                return
            }

            if (!calculator.displayValue.includes(dot)) {
                calculator.displayValue += dot;
            }
        }

        function handleOperator(nextOperator) {
            const { firstOperand, displayValue, operator } = calculator;
            const inputValue = parseFloat(displayValue);

            if (operator && calculator.waitingForSecondOperand)  {
                calculator.operator = nextOperator;
                return;
            }

            if (firstOperand == null && !isNaN(inputValue)) {
                calculator.firstOperand = inputValue;
            } else if (operator) {
                const result = calculate(firstOperand, inputValue, operator);

                calculator.displayValue = `${parseFloat(result.toFixed(7))}`;
                calculator.firstOperand = result;
            }

            calculator.waitingForSecondOperand = true;
            calculator.operator = nextOperator;
        }

        function calculate(firstOperand, secondOperand, operator) {
            if (operator === '+') {
                return firstOperand + secondOperand;
            } else if (operator === '-') {
                return firstOperand - secondOperand;
            } else if (operator === '*') {
                return firstOperand * secondOperand;
            } else if (operator === '/') {
                return firstOperand / secondOperand;
            }

            return secondOperand;
        }

        function resetCalculator() {
            calculator.displayValue = '0';
            calculator.firstOperand = null;
            calculator.waitingForSecondOperand = false;
            calculator.operator = null;
        }

        function updateDisplay() {
            const display = document.querySelector('.calculator-screen');
            display.value = calculator.displayValue;
        }

        updateDisplay();

        const keys = document.querySelector('.calculator-keys');
        keys.addEventListener('click', (event) => {
            const { target } = event;
            const { value } = target;
            if (!target.matches('button')) {
                return;
            }

            switch (value) {
                case '+':
                case '-':
                case '*':
                case '/':
                case '=':
                    handleOperator(value);
                    break;
                case '.':
                    inputDecimal(value);
                    break;
                case 'all-clear':
                    resetCalculator();
                    break;
                default:
                    if (Number.isInteger(parseFloat(value))) {
                        inputDigit(value);
                    }
            }

            updateDisplay();
        });
    </script>
</body>
</html>
