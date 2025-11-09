# Ex06 BMI Calculator
## Date:09-11-2025

## AIM
To create a BMI calculator using React Router 

## ALGORITHM
### STEP 1 State Initialization
Manage the current page (Home or Calculator) using React Router.

### STEP 2 User Input
Accept weight and height inputs from the user.

### STEP 3 BMI Calculation
Calculate the BMI based on user input.

### STEP 4 Categorization
Classify the BMI result into categories (Underweight, Normal weight, Overweight, Obesity).

### STEP 5 Navigation
Navigate between pages using React Router.

## PROGRAM
## App.jsx:
```

import React, { useState, useMemo } from "react";
import "./App.css";

export default function BMICalculator() {
  
  const [units, setUnits] = useState("metric");
  const [weight, setWeight] = useState(""); 
  const [height, setHeight] = useState(""); 
  const [error, setError] = useState("");
  const [touched, setTouched] = useState({ weight: false, height: false });

  
  const parseNum = (v) => {
    if (v === "" || v === null || v === undefined) return NaN;
    
    return Number(String(v).replace(/,/g, "").trim());
  };

  
  const { bmi, category, advice } = useMemo(() => {
    const w = parseNum(weight);
    const h = parseNum(height);

    if (Number.isNaN(w) || Number.isNaN(h) || w <= 0 || h <= 0) {
      return { bmi: null, category: null, advice: null };
    }

    
    let kg = w;
    let meters = h;
    if (units === "metric") {
      
      meters = h / 100;
    } else {
      
      kg = w * 0.45359237;
      meters = h * 0.0254;
    }

    
    const rawBmi = kg / (meters * meters);
    const rounded = Math.round(rawBmi * 10) / 10;
    let cat = "";
    let adviceText = "";
    if (rounded < 18.5) {
      cat = "Underweight";
      adviceText = "You are below the healthy weight range. Consider a balanced, calorie-rich diet and check with a healthcare professional.";
    } else if (rounded < 25) {
      cat = "Normal";
      adviceText = "Great — your BMI is in the healthy range. Keep up a balanced diet and regular activity.";
    } else if (rounded < 30) {
      cat = "Overweight";
      adviceText = "You are above the recommended range. Consider adjusting diet and increasing physical activity; consult a professional if needed.";
    } else {
      cat = "Obese";
      adviceText = "BMI indicates obesity. It's advisable to consult a healthcare professional to create a safe weight plan.";
    }

    return { bmi: rounded, category: cat, advice: adviceText };
  }, [weight, height, units]);

  // Input validation logic to show helpful errors
  const validate = () => {
    setError("");
    const w = parseNum(weight);
    const h = parseNum(height);
    if (Number.isNaN(w) || w <= 0) {
      setError("Please enter a valid positive weight.");
      return false;
    }
    if (Number.isNaN(h) || h <= 0) {
      setError("Please enter a valid positive height.");
      return false;
    }
    // realistic limits
    if (units === "metric") {
      if (w < 10 || w > 500) {
        setError("Weight (kg) seems unrealistic. Enter between 10 and 500 kg.");
        return false;
      }
      if (h < 50 || h > 300) {
        setError("Height (cm) seems unrealistic. Enter between 50 and 300 cm.");
        return false;
      }
    } else {
      if (w < 22 || w > 1100) {
        setError("Weight (lb) seems unrealistic. Enter between 22 and 1100 lb.");
        return false;
      }
      if (h < 20 || h > 120) {
        setError("Height (in) seems unrealistic. Enter between 20 and 120 in.");
        return false;
      }
    }
    setError("");
    return true;
  };

  const handleSubmit = (e) => {
    e?.preventDefault();
    setTouched({ weight: true, height: true });
    if (!validate()) return;
  };

  const resetForm = () => {
    setWeight("");
    setHeight("");
    setTouched({ weight: false, height: false });
    setError("");
  };

  return (
    <div className="bmi-wrap">
      <form className="bmi-card" onSubmit={handleSubmit} noValidate>
        <h2 className="bmi-title">BMI Calculator</h2>

        <div className="unit-toggle" role="radiogroup" aria-label="Unit system">
          <label className={`unit ${units === "metric" ? "selected" : ""}`}>
            <input
              type="radio"
              name="units"
              value="metric"
              checked={units === "metric"}
              onChange={() => setUnits("metric")}
            />
            Metric (kg, cm)
          </label>
          <label className={`unit ${units === "imperial" ? "selected" : ""}`}>
            <input
              type="radio"
              name="units"
              value="imperial"
              checked={units === "imperial"}
              onChange={() => setUnits("imperial")}
            />
            Imperial (lb, in)
          </label>
        </div>

        <div className="fields">
          <label className="field">
            <span className="label-text">Weight ({units === "metric" ? "kg" : "lb"})</span>
            <input
              inputMode="decimal"
              pattern="[0-9]*"
              type="text"
              value={weight}
              placeholder={units === "metric" ? "e.g. 70" : "e.g. 154"}
              onChange={(e) => setWeight(e.target.value)}
              onBlur={() => setTouched((t) => ({ ...t, weight: true }))}
              aria-invalid={touched.weight && (weight === "" || Number.isNaN(Number(weight)))}
            />
          </label>

          <label className="field">
            <span className="label-text">Height ({units === "metric" ? "cm" : "in"})</span>
            <input
              inputMode="decimal"
              pattern="[0-9]*"
              type="text"
              value={height}
              placeholder={units === "metric" ? "e.g. 175" : "e.g. 69"}
              onChange={(e) => setHeight(e.target.value)}
              onBlur={() => setTouched((t) => ({ ...t, height: true }))}
              aria-invalid={touched.height && (height === "" || Number.isNaN(Number(height)))}
            />
          </label>
        </div>

        {error && <div className="error" role="alert">{error}</div>}

        <div className="actions">
          <button type="submit" className="btn primary">Calculate</button>
          <button type="button" className="btn ghost" onClick={resetForm}>Reset</button>
        </div>

        <div className="result" aria-live="polite">
          {bmi === null ? (
            <p className="result-placeholder">Enter your values and press <strong>Calculate</strong>.</p>
          ) : (
            <div className="result-box">
              <div className="bmi-value">
                <span className="num">{bmi}</span>
                <span className="unit-text">kg/m²</span>
              </div>
              <div className={`bmi-category ${category ? category.toLowerCase() : ""}`}>
                <strong>{category}</strong>
              </div>
              <p className="advice">{advice}</p>
            </div>
          )}
        </div>

        <div className="help">
          <p>Note: BMI is a screening tool and doesn't account for muscle mass or distribution. For personalised advice, consult a healthcare professional.</p>
          <h1>Name: Sharon Harshini L M</h1>
        </div>
      </form>
    </div>
  );
}
```
## App.css:
```

* { box-sizing: border-box; }
body, html, #root { height: 100%; margin: 0; font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; }

.app-root {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, #f6f9ff 0%, #fff 50%);
  padding: 40px 20px;
}


.bmi-wrap { width: 100%; max-width: 760px; }
.bmi-card {
  background: linear-gradient(180deg, #ffffff, #fbfdff);
  border-radius: 16px;
  padding: 28px;
  box-shadow: 0 12px 30px rgba(32, 45, 80, 0.08);
  border: 1px solid rgba(32,45,80,0.04);
}


.bmi-title {
  margin: 0 0 18px 0;
  font-size: 1.6rem;
  color: #21304a;
  letter-spacing: -0.2px;
  text-align: center;
}


.unit-toggle {
  display: flex;
  gap: 12px;
  justify-content: center;
  margin-bottom: 18px;
}

.unit {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 14px;
  border-radius: 999px;
  font-size: 0.95rem;
  color: #445566;
  background: transparent;
  cursor: pointer;
  border: 1px solid transparent;
  transition: all 0.2s ease;
}

.unit input { display: none; }

.unit.selected {
  background: linear-gradient(90deg, #ff9a9e 0%, #fecfef 100%);
  color: white;
  box-shadow: 0 6px 16px rgba(255, 105, 135, 0.12);
  border: 1px solid rgba(255,255,255,0.16);
}


.fields {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-bottom: 12px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.label-text {
  font-size: 0.9rem;
  color: #556677;
}


.field input[type="text"] {
  padding: 12px 14px;
  border-radius: 10px;
  border: 1px solid rgba(35,47,63,0.08);
  background: linear-gradient(180deg,#fff,#fafcff);
  font-size: 1rem;
  outline: none;
  transition: box-shadow 0.15s ease, transform 0.15s ease;
}

.field input[type="text"]:focus {
  box-shadow: 0 6px 18px rgba(34, 124, 255, 0.12);
  transform: translateY(-1px);
  border-color: rgba(34,124,255,0.25);
}

.error {
  margin-top: 8px;
  color: #9b1c1c;
  background: #fff5f5;
  border: 1px solid rgba(155,28,28,0.08);
  padding: 10px 12px;
  border-radius: 8px;
  font-size: 0.95rem;
}

.actions {
  display: flex;
  gap: 12px;
  justify-content: center;
  margin-top: 12px;
  margin-bottom: 18px;
}

.btn {
  padding: 10px 16px;
  border-radius: 10px;
  font-weight: 600;
  font-size: 0.98rem;
  cursor: pointer;
  border: none;
}

.btn.primary {
  background: linear-gradient(90deg,#5ab0ff,#6bb6ff);
  color: white;
  box-shadow: 0 8px 20px rgba(91,160,255,0.18);
}

.btn.ghost {
  background: transparent;
  color: #445566;
  border: 1px solid rgba(68,85,102,0.08);
}


.result {
  margin-top: 6px;
  display: flex;
  justify-content: center;
  align-items: center;
}


.result-placeholder {
  color: #667788;
  font-size: 0.98rem;
}


.result-box {
  display: flex;
  align-items: center;
  gap: 18px;
  background: linear-gradient(90deg, #ffffff, #f7fbff);
  padding: 14px 18px;
  border-radius: 12px;
  border: 1px solid rgba(33,48,74,0.04);
  box-shadow: 0 8px 24px rgba(32,45,80,0.04);
  width: 100%;
  max-width: 640px;
}

.bmi-value {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-width: 120px;
  padding-right: 10px;
  border-right: 1px solid rgba(34,47,63,0.04);
}

.bmi-value .num {
  font-size: 2.4rem;
  font-weight: 700;
  color: #21304a;
  letter-spacing: -1px;
}

.unit-text {
  font-size: 0.95rem;
  color: #667788;
  margin-top: 4px;
}


.bmi-category {
  font-size: 1rem;
  padding: 8px 12px;
  border-radius: 999px;
  background: #eef6ff;
  color: #1b4f9a;
  font-weight: 700;
}

.bmi-category.underweight { background: #fff7e6; color: #b36b00; }
.bmi-category.normal { background: #e9fff1; color: #0a7a3a; }
.bmi-category.overweight { background: #fff5ea; color: #b35700; }
.bmi-category.obese { background: #fff0f0; color: #a51c1c; }

.advice {
  color: #445566;
  margin: 6px 0 0 0;
  font-size: 0.95rem;
}

.help {
  margin-top: 14px;
  color: #7990a6;
  font-size: 0.9rem;
  text-align: center;
}

@media (max-width: 680px) {
  .fields { grid-template-columns: 1fr; }
  .result-box { flex-direction: column; align-items: flex-start; gap: 10px; }
  .bmi-value { width: 100%; border-right: none; border-bottom: 1px solid rgba(34,47,63,0.04); padding-bottom: 10px; }
}
```

## OUTPUT
<img width="1919" height="1060" alt="image" src="https://github.com/user-attachments/assets/7ec95522-ea9b-4374-a965-5deeebe38ff2" />


## RESULT
The program for creating BMI Calculator using React Router is executed successfully.
