# calculator-aplikasi
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Simple Calculator</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      min-height: 100vh;
      margin: 0;
      display: grid;
      place-items: center;
      background: #eef2f7;
      font-family: system-ui, sans-serif;
    }

    .calculator {
      width: min(320px, calc(100vw - 32px));
      padding: 20px;
      border-radius: 16px;
      background: #1f2937;
      box-shadow: 0 12px 30px #17203333;
    }

    #display {
      width: 100%;
      height: 72px;
      margin-bottom: 16px;
      padding: 12px;
      border: 0;
      border-radius: 10px;
      background: #111827;
      color: white;
      font: inherit;
      font-size: 2rem;
      text-align: right;
    }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }

    button {
      min-height: 56px;
      border: 0;
      border-radius: 10px;
      background: #374151;
      color: white;
      font: inherit;
      font-size: 1.2rem;
      cursor: pointer;
    }

    button:hover {
      background: #4b5563;
    }

    button:focus-visible {
      outline: 3px solid #93c5fd;
      outline-offset: 2px;
    }

    button.operator {
      background: #2563eb;
    }

    button.operator:hover {
      background: #1d4ed8;
    }

    button.utility {
      background: #6b7280;
    }

    button.equals {
      grid-row: span 2;
      background: #16a34a;
    }

    button.equals:hover {
      background: #15803d;
    }

    button.zero {
      grid-column: span 2;
    }
  </style>
</head>
<body>
  <main class="calculator" aria-label="Calculator">
    <input id="display" value="0" aria-label="Calculator display" readonly>
    <div class="buttons">
      <button class="utility" data-action="clear">AC</button>
      <button class="utility" data-action="delete" aria-label="Delete last digit">DEL</button>
      <button class="operator" data-operation="÷">÷</button>
      <button class="operator" data-operation="×">×</button>

      <button data-digit="7">7</button>
      <button data-digit="8">8</button>
      <button data-digit="9">9</button>
      <button class="operator" data-operation="−">−</button>

      <button data-digit="4">4</button>
      <button data-digit="5">5</button>
      <button data-digit="6">6</button>
      <button class="operator" data-operation="+">+</button>

      <button data-digit="1">1</button>
      <button data-digit="2">2</button>
      <button data-digit="3">3</button>
      <button class="equals" data-action="equals">=</button>

      <button class="zero" data-digit="0">0</button>
      <button data-action="decimal">.</button>
    </div>
  </main>

  <script>
    const display = document.querySelector("#display");
    const buttons = document.querySelector(".buttons");

    let current = "0";
    let previous = null;
    let operation = null;
    let startNewNumber = false;

    function updateDisplay() {
      display.value = current;
    }

    function enterDigit(digit) {
      if (current === "Error" || startNewNumber) {
        current = digit;
        startNewNumber = false;
      } else {
        current = current === "0" ? digit : current + digit;
      }
      updateDisplay();
    }

    function enterDecimal() {
      if (current === "Error" || startNewNumber) {
        current = "0.";
        startNewNumber = false;
      } else if (!current.includes(".")) {
        current += ".";
      }
      updateDisplay();
    }

    function calculate() {
      if (operation === null || previous === null) return;

      const a = Number(previous);
      const b = Number(current);
      let result;

      switch (operation) {
        case "+": result = a + b; break;
        case "−": result = a - b; break;
        case "×": result = a * b; break;
        case "÷": result = b === 0 ? NaN : a / b; break;
      }

      current = Number.isFinite(result)
        ? String(Number(result.toPrecision(10)))
        : "Error";

      previous = null;
      operation = null;
      startNewNumber = true;
      updateDisplay();
    }

    function chooseOperation(nextOperation) {
      if (current === "Error") return;

      if (operation !== null && !startNewNumber) {
        calculate();
      }

      previous = current;
      operation = nextOperation;
      startNewNumber = true;
    }

    function clear() {
      current = "0";
      previous = null;
      operation = null;
      startNewNumber = false;
      updateDisplay();
    }

    function deleteLastDigit() {
      if (current === "Error" || startNewNumber) {
        current = "0";
        startNewNumber = false;
      } else {
        current = current.length > 1 ? current.slice(0, -1) : "0";
      }
      updateDisplay();
    }

    buttons.addEventListener("click", (event) => {
      const button = event.target.closest("button");
      if (!button) return;

      if (button.dataset.digit !== undefined) enterDigit(button.dataset.digit);
      if (button.dataset.operation) chooseOperation(button.dataset.operation);
      if (button.dataset.action === "decimal") enterDecimal();
      if (button.dataset.action === "equals") calculate();
      if (button.dataset.action === "clear") clear();
      if (button.dataset.action === "delete") deleteLastDigit();
    });

    document.addEventListener("keydown", (event) => {
      if (/^[0-9]$/.test(event.key)) enterDigit(event.key);
      else if (event.key === ".") enterDecimal();
      else if (event.key === "+") chooseOperation("+");
      else if (event.key === "-") chooseOperation("−");
      else if (event.key === "*") chooseOperation("×");
      else if (event.key === "/") {
        event.preventDefault();
        chooseOperation("÷");
      } else if (event.key === "Enter" || event.key === "=") calculate();
      else if (event.key === "Backspace") deleteLastDigit();
      else if (event.key === "Escape") clear();
    });
  </script>
</body>
</html>
