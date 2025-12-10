# Copilot Instructions for Fish Weight Calculator

## Project Overview
This is a scientific web application for calculating fish weight from length using allometric relationships (W = a·L^b). Two implementations exist:
- `index.html`: CSV-powered multi-species calculator with data visualization
- `index2.html`: Manual calibration interface for field data entry

Both use **log-log linear regression** (least squares method) to calculate coefficients from empirical data.

## Architecture & Data Flow

### Core Mathematical Model
All calculations use the **power law relationship**: `Weight = a * Length^b`

Linearization via logarithmic transformation:
- Transform: `ln(W) = ln(a) + b·ln(L)`
- Apply linear regression on log-transformed data
- Extract: `b = slope`, `a = e^intercept`

**Key Function Pattern** (found in both files):
```javascript
function calcularRegressaoLinear(pontos) {
    let sumX = 0, sumY = 0, sumXY = 0, sumXX = 0;
    for (let ponto of pontos) {
        let x = Math.log(ponto.L);  // ln(Length)
        let y = Math.log(ponto.W);  // ln(Weight)
        // accumulate sums for regression
    }
    let slope = (n * sumXY - sumX * sumY) / (n * sumXX - sumX * sumX);
    let intercept = (sumY - slope * sumX) / n;
    return { a: Math.exp(intercept), b: slope };
}
```

### Data Sources
- `fish_data.csv`: Large multi-species dataset (4000+ records) with columns: `species,length,weight,w_l_ratio`
- `tucunare.csv`: Cichla species dataset (46 records) with structure: `Species,Length,Weight`

## Implementation Differences

### `index.html` - Production Calculator
- **Workflow**: Upload CSV → Auto-calculate all species coefficients → Select species → Calculate weight
- **State Management**: `baseDeDados` object stores `{a, b, n}` per species; `dadosCsvBrutos` stores raw CSV data
- **UI Pattern**: Disabled calculator until CSV loaded (opacity + pointer-events control)
- **Special Features**: 
  - Opens new windows for data/coefficient tables using `window.open()` + document.write()
  - XSS protection via `escaparHtml()` function for user-generated content
  - Dynamic dropdown population from CSV species list

### `index2.html` - Field Research Tool  
- **Workflow**: Paste length/weight pairs → Calibrate model → Enter new length → Get prediction
- **State Management**: Global `modeloA` and `modeloB` variables (initially null)
- **Input Format**: Textarea with comma-separated pairs (one per line): `10, 15` format
- **UI Pattern**: Two-column grid layout (calibration | prediction), collapses to single column on mobile

## Development Conventions

### Language & Localization
- All UI text, comments, and variables in **Portuguese** (`pt-BR`)
- Function names use Portuguese verbs: `calcular`, `processar`, `abrir`
- Scientific terminology uses Portuguese: "coeficientes", "regressão", "alometria"

### Styling Patterns
- CSS custom properties in `index2.html` (`:root` variables)
- Inline styles in `index.html` (no external CSS)
- Consistent color scheme: `#0077be` (blue) for primary actions, `#16a34a` (green) for success states
- Result boxes use light backgrounds (`#e6f7ff`, `#f1f5f9`) with accent borders

### Data Validation
Always validate before calculations:
```javascript
if (especie && comprimento > 0 && peso > 0) { /* process */ }
if (!baseDeDados[especie] || !comp) { alert("..."); return; }
```

### Number Formatting
- Coefficients: `.toFixed(4)` or `.toFixed(5)` for `a`, `.toFixed(3)` for `b`
- Weight results: `.toFixed(2)` for grams, convert to kg if >= 1000g

## Working with This Codebase

### Adding New Species Data
CSV must have header row with `species,length,weight` (case-insensitive). Skip if `length ≤ 0` or `weight ≤ 0`.

### Modifying Calculations
The regression algorithm is identical in both files—update both if changing math. Key variables:
- `sumX`, `sumY`, `sumXY`, `sumXX`: accumulation sums for regression
- `n`: sample count
- Always use `Math.log()` (natural log, not `Math.log10()`)

### Testing Changes
1. Open HTML file directly in browser (no build step needed)
2. For `index.html`: Use `fish_data.csv` or `tucunare.csv` as test data
3. For `index2.html`: Test with sample data already in textarea

### Common Pitfalls
- **Don't use `Math.log10()`**: Regression assumes natural logarithm
- **CSV parsing**: Uses `.split(',')` without quotes handling—avoid commas in species names
- **Window.open blockers**: Popup blockers may prevent data table displays
- **Null checks**: Always verify model calibration before prediction in `index2.html`

## No Build System
Pure HTML/CSS/JavaScript—no frameworks, no bundlers. Edit and refresh browser to test.
