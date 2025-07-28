# hashira


<!DOCTYPE html>
<html>
<head>
  <title>Shamir's Secret Sharing Solution</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 800px;
      margin: 0 auto;
      padding: 20px;
      line-height: 1.6;
    }
    .container {
      background-color: #f5f5f5;
      padding: 20px;
      border-radius: 8px;
      margin-bottom: 20px;
    }
    button {
      background-color: #4CAF50;
      color: white;
      border: none;
      padding: 10px 15px;
      border-radius: 4px;
      cursor: pointer;
      margin-top: 10px;
    }
    button:hover {
      background-color: #45a049;
    }
    .result {
      margin-top: 20px;
      padding: 15px;
      background-color: #e8f5e9;
      border-radius: 4px;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>Shamir's Secret Sharing Solution</h1>
  
  <div class="container">
    <h2>Test Case 1</h2>
    <button onclick="solveTestCase(1)">Solve Test Case 1</button>
    <div id="result1" class="result"></div>
  </div>

  <div class="container">
    <h2>Test Case 2</h2>
    <button onclick="solveTestCase(2)">Solve Test Case 2</button>
    <div id="result2" class="result"></div>
  </div>

  <script>
    const testCases = {
      1: {
        "keys": {
          "n": 4,
          "k": 3
        },
        "1": {
          "base": "10",
          "value": "4"
        },
        "2": {
          "base": "2",
          "value": "111"
        },
        "3": {
          "base": "10",
          "value": "12"
        },
        "6": {
          "base": "4",
          "value": "213"
        }
      },
      2: {
        "keys": {
          "n": 10,
          "k": 7
        },
        "1": {
          "base": "6",
          "value": "13444211440455345511"
        },
        "2": {
          "base": "15",
          "value": "aed7015a346d63"
        },
        "3": {
          "base": "15",
          "value": "6aeeb69631c227c"
        },
        "4": {
          "base": "16",
          "value": "e1b5e05623d881f"
        },
        "5": {
          "base": "8",
          "value": "316034514573652620673"
        },
        "6": {
          "base": "3",
          "value": "2122212201122002221120200210011020220200"
        },
        "7": {
          "base": "3",
          "value": "20120221122211000100210021102001201112121"
        },
        "8": {
          "base": "6",
          "value": "20220554335330240002224253"
        },
        "9": {
          "base": "12",
          "value": "45153788322a1255483"
        },
        "10": {
          "base": "7",
          "value": "1101613130313526312514143"
        }
      }
    };

    function parseBaseValue(value, base) {
      const digits = '0123456789abcdefghijklmnopqrstuvwxyz';
      base = parseInt(base);
      
      let result = 0;
      let power = 1;
      
      for (let i = value.length - 1; i >= 0; i--) {
        const digit = digits.indexOf(value[i].toLowerCase());
        if (digit === -1 || digit >= base) {
          throw new Error(`Invalid digit '${value[i]}' for base ${base}`);
        }
        result += digit * power;
        power *= base;
      }
      
      return result;
    }

    function lagrangeInterpolation(points, x) {
      let result = 0;
      const n = points.length;
      
      for (let i = 0; i < n; i++) {
        let term = points[i].y;
        for (let j = 0; j < n; j++) {
          if (i !== j) {
            term *= (x - points[j].x) / (points[i].x - points[j].x);
          }
        }
        result += term;
      }
      
      return result;
    }

    function solvePolynomial(data) {
      const n = data.keys.n;
      const k = data.keys.k;
      const points = [];
      
      for (let key in data) {
        if (key !== 'keys') {
          const point = {
            x: parseInt(key),
            y: parseBaseValue(data[key].value, data[key].base)
          };
          points.push(point);
        }
      }
      
      if (points.length < k) {
        throw new Error("Not enough points to determine the polynomial");
      }
      
      const c = lagrangeInterpolation(points, 0);
      return Math.round(c);
    }

    function solveTestCase(caseNum) {
      try {
        const jsonData = testCases[caseNum];
        const secret = solvePolynomial(jsonData);
        
        document.getElementById(`result${caseNum}`).innerHTML = 
          `The secret constant term (c) is: <span style="color:#2e7d32">${secret}</span>`;
      } catch (error) {
        document.getElementById(`result${caseNum}`).innerHTML = 
          `Error: ${error.message}`;
      }
    }
  </script>
</body>
</html>
