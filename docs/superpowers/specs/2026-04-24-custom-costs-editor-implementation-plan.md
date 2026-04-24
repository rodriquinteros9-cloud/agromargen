# Custom Costs Editor - Implementation Plan
**Date:** 2026-04-24  
**Status:** Implementation Phase  
**Target:** Complete feature with full integration

---

## Phase Overview

The implementation is divided into **3 Phases** with **15 concrete tasks**:

### Phase 1: Foundation & Data Layer (Tasks 1-4)
- Set up localStorage schema
- Create data structures and helpers
- Wire routing system

### Phase 2: UI & Editor Interface (Tasks 5-10)
- Build HTML markup for custom costs view
- Implement form interactions (add/delete rows)
- Real-time calculations
- Styling to match existing design system

### Phase 3: Integration & Polish (Tasks 11-15)
- Wire button in existing cost panel
- Toggle active scenario logic
- Rentabilidad recalculation
- PDF export
- Testing & refinement

---

## Detailed Task Breakdown

### ⚙️ PHASE 1: Foundation & Data Layer

#### Task 1: Define localStorage Schema & Helpers
**Complexity:** ⭐ Low | **Est. Time:** 30 min  
**Deliverables:**
- Constants for localStorage keys
- Data validation functions for Insumo and Labor objects
- UUID generator for row IDs
- Example/default data structure

**Code location:** Will add to main HTML `<script>` section, `customCosts` object

```javascript
// Structure to implement:
const customCosts = {
  // localStorage key builder
  getKey: (crop) => `agromargen_customCosts_${crop}`,
  
  // Initialize empty scenario
  createEmptyScenario: (crop, name) => { /* ... */ },
  
  // Validation functions
  validateInsumo: (insumo) => { /* ... */ },
  validateLabor: (labor) => { /* ... */ },
  validateScenario: (scenario) => { /* ... */ },
  
  // Calculation helpers
  calculateInsumoRowCost: (cantidad, valorUSD) => cantidad * valorUSD,
  calculateLaborRowCost: (cantidad, precioUnitario) => cantidad * precioUnitario,
  calculateScenarioTotals: (scenario) => { /* ... */ },
  
  // localStorage CRUD
  saveScenario: (crop, scenario) => { /* ... */ },
  loadScenario: (crop, scenarioId) => { /* ... */ },
  loadAllScenarios: (crop) => { /* ... */ },
  deleteScenario: (crop, scenarioId) => { /* ... */ },
  
  // Active scenario toggle
  setActiveScenario: (crop, scenarioId) => { /* ... */ },
  getActiveScenario: (crop) => { /* ... */ },
  clearActiveScenario: (crop) => { /* ... */ }
};
```

**Acceptance Criteria:**
- ✅ All validation functions return boolean or error message
- ✅ Calculation functions are pure (no side effects)
- ✅ localStorage functions handle parsing/stringifying JSON
- ✅ UUID generation produces unique IDs

---

#### Task 2: Create Router Entry for Custom Costs View
**Complexity:** ⭐ Low | **Est. Time:** 20 min  
**Deliverables:**
- Route handler for `#costos-personalizados`
- Navigation logic to hide/show views
- Back button functionality

**Code location:** Existing routing section in HTML `<script>`

```javascript
// Add route handler:
// When hash changes to #costos-personalizados:
// 1. Hide existing views (home, ventanas, etc.)
// 2. Show custom costs editor view
// 3. Load data for selected crop
// 4. Focus on first input
```

**Acceptance Criteria:**
- ✅ Route is triggered by `window.location.hash = '#costos-personalizados'`
- ✅ Back button returns to previous view
- ✅ Works with browser back/forward buttons
- ✅ Selected crop context is preserved

---

#### Task 3: Add HTML Markup for Custom Costs View
**Complexity:** ⭐⭐ Medium | **Est. Time:** 45 min  
**Deliverables:**
- Complete HTML structure (header, 3 sections, footer)
- Input form elements (text, number, select dropdowns)
- Hidden template rows for cloning
- Totals display section

**Code location:** New `<div id="customCostsView">` in main HTML body

```html
Structure to create:
<div id="customCostsView" style="display: none;">
  <!-- Header -->
  <header class="custom-costs-header">
    <button id="backBtn">← Volver</button>
    <h2>INGRESAR COSTOS PERSONALIZADOS</h2>
    <p id="cropContext">Cultivo: SOJA 24/25</p>
  </header>
  
  <!-- Main form -->
  <form id="customCostsForm">
    <!-- Section 1: Herbicidas -->
    <section id="herbicidasSection">
      <h3>INSUMOS - HERBICIDAS</h3>
      <button type="button" id="addHerbicida">+ Agregar fila</button>
      <div id="herbicidasRows" class="rows-container">
        <!-- Rows inserted here -->
      </div>
    </section>
    
    <!-- Section 2: Insecticidas -->
    <section id="insecticidasSection">
      <!-- Similar structure -->
    </section>
    
    <!-- Section 3: Fertilizantes -->
    <section id="fertilizantesSection">
      <!-- Similar structure -->
    </section>
    
    <!-- Section 4: Labores -->
    <section id="laboresSection">
      <!-- Similar structure -->
    </section>
    
    <!-- Section 5: Resumen (read-only) -->
    <section id="resumenSection" class="read-only">
      <h3>RESUMEN DE COSTOS</h3>
      <div id="totalesDisplay">
        <!-- Totals filled via JavaScript -->
      </div>
    </section>
  </form>
  
  <!-- Footer buttons -->
  <footer class="custom-costs-footer">
    <button id="clearBtn" type="button">Limpiar</button>
    <button id="exportPdfBtn" type="button">Exportar PDF</button>
    <button id="saveBtn" type="button" class="primary">Guardar Costos Personalizados</button>
  </footer>
  
  <!-- Hidden template for cloning -->
  <template id="insumoRowTemplate">
    <div class="insumo-row" data-row-id="">
      <input type="text" class="nombre" placeholder="Nombre" />
      <select class="tipo">
        <option>Barbecho</option>
        <option>Pre-siembra</option>
        <option>Pre-emergencia</option>
        <!-- etc -->
      </select>
      <input type="number" class="cantidad" placeholder="Cantidad" />
      <input type="number" class="valorUSD" placeholder="USD/unidad" />
      <input type="number" class="costoUSD" readonly />
      <button type="button" class="deleteBtn">×</button>
    </div>
  </template>
  
  <!-- Template for labor row -->
  <template id="laborRowTemplate">
    <div class="labor-row" data-row-id="">
      <input type="text" class="tarea" placeholder="Tarea" />
      <input type="number" class="cantidad" placeholder="Cantidad" />
      <input type="number" class="precioUnitario" placeholder="Precio unit." />
      <input type="number" class="costoUSD" readonly />
      <button type="button" class="deleteBtn">×</button>
    </div>
  </template>
</div>
```

**Acceptance Criteria:**
- ✅ All 5 sections rendered correctly
- ✅ Form validates when submitted
- ✅ Templates cloned properly when adding rows
- ✅ Readonly fields not editable
- ✅ Delete buttons present on each row

---

#### Task 4: Create CSS Styling for Custom Costs View
**Complexity:** ⭐⭐ Medium | **Est. Time:** 40 min  
**Deliverables:**
- Consistent styling matching existing design system
- Responsive table/form layout
- Button states and interactions
- Read-only section styling

**Code location:** New `<style>` section in HTML `<head>` or existing style block

```css
/* Key classes to style:
.custom-costs-header
.custom-costs-view section
.rows-container
.insumo-row
.labor-row
.deleteBtn
.read-only
.custom-costs-footer
*/
```

**Design constraints:**
- Use existing color variables: `--ink`, `--brand`, `--ok`, `--warn`
- Match font: Manrope
- Spacing: consistent with existing panels (12px-24px gaps)
- Input styling: similar to existing form inputs

**Acceptance Criteria:**
- ✅ Matches visual mockup from design spec
- ✅ Inputs have clear focus states
- ✅ Delete buttons easily clickable (target size ≥44px)
- ✅ Responsive on mobile (single column)
- ✅ Read-only fields visually distinct

---

### 📝 PHASE 2: UI & Editor Interface

#### Task 5: Implement "Add Row" Functionality
**Complexity:** ⭐⭐ Medium | **Est. Time:** 45 min  
**Deliverables:**
- Click handlers for all "Agregar fila" buttons
- Dynamic row generation from templates
- Auto-focus first input on new row
- Event delegation for new rows

**Code location:** `customCosts.addRow()` function and listeners

```javascript
// Pseudo-code:
function addInsumoRow(category) { // category: 'herbicidas', 'insecticidas', or 'fertilizantes'
  1. Clone template #insumoRowTemplate
  2. Generate unique row ID
  3. Set data-row-id on cloned element
  4. Attach delete listener to deleteBtn
  5. Attach input listeners for cost calculation
  6. Insert into DOM
  7. Auto-focus first input
  8. Return row element
}

function addLaborRow() {
  1. Clone template #laborRowTemplate
  2. Generate unique row ID
  3. Set data-row-id on cloned element
  4. Attach delete listener
  5. Attach input listeners for cost calculation
  6. Insert into DOM
  7. Auto-focus first input
  8. Return row element
}
```

**Acceptance Criteria:**
- ✅ New rows append correctly to each section
- ✅ Each row has unique ID
- ✅ First input auto-focused (UX improvement)
- ✅ Delete buttons work on dynamically created rows
- ✅ No console errors on repeated clicks

---

#### Task 6: Implement "Delete Row" Functionality
**Complexity:** ⭐ Low | **Est. Time:** 25 min  
**Deliverables:**
- Delete button handlers
- Confirmation dialog (optional but recommended)
- Row removal and DOM cleanup
- Recalculation after delete

**Code location:** `customCosts.deleteRow()` function

```javascript
function deleteRow(rowId, section) {
  1. Find row element by data-row-id
  2. Show confirmation: "¿Eliminar esta fila?"
  3. If confirmed:
     - Remove row from DOM
     - Call calculateTotals()
     - Show toast: "Fila eliminada"
  4. If cancelled: do nothing
}
```

**Acceptance Criteria:**
- ✅ Confirmation dialog prevents accidental deletion
- ✅ Row removed from DOM
- ✅ Totals recalculate after deletion
- ✅ Can delete rows from any section
- ✅ At least one row remains (or section becomes empty cleanly)

---

#### Task 7: Implement Real-Time Cost Calculation
**Complexity:** ⭐⭐ Medium | **Est. Time:** 40 min  
**Deliverables:**
- Input listeners on cantidad and valorUSD fields
- Real-time calculation of row costs (cantidad × valorUSD)
- Totals recalculation across all sections
- Update RESUMEN section dynamically

**Code location:** `customCosts.calculateTotals()` and row input listeners

```javascript
// On any input change:
1. Get updated value (cantidad or valorUSD)
2. Calculate: costoUSD = cantidad × valorUSD
3. Update readonly costoUSD field
4. Call calculateTotals()
5. Update RESUMEN display

function calculateTotals() {
  1. Sum all insumo rows: totalInsumos
  2. Sum all labor rows: totalLabores
  3. gastosDirectos = totalInsumos + totalLabores
  4. Add alquiler + adEstr (pulled from estimados)
  5. totalGastos = gastosDirectos + alquiler + adEstr
  6. Update RESUMEN display
}
```

**Acceptance Criteria:**
- ✅ Costs update immediately as user types (no delay)
- ✅ Totals recalculate correctly on every change
- ✅ Currency formatting applied (2 decimals)
- ✅ RESUMEN section updates live
- ✅ Math is accurate (test with known examples)

---

#### Task 8: Implement Data Validation
**Complexity:** ⭐⭐ Medium | **Est. Time:** 35 min  
**Deliverables:**
- Validation rules for each field type
- Error messages and UI feedback
- Prevent save if validation fails
- Validation on input (immediate feedback)

**Code location:** `customCosts.validateScenario()` and field validators

```javascript
Validation rules:
- nombre (text): required, non-empty, max 100 chars
- tipo (select): required, valid option
- cantidad (number): required, positive, ≤ 999
- valorUSD (number): required, positive, ≤ 99999
- tarea (text): required, non-empty, max 100 chars
- precioUnitario (number): required, positive

Error display:
- Red border on invalid field
- Error message below field (small text, red color)
- Clear on valid input

Toast notifications for validation:
- Show toast before attempt to save
- List all errors (up to 5)
```

**Acceptance Criteria:**
- ✅ Fields show error state immediately after input
- ✅ Cannot save if any field invalid
- ✅ Error messages are clear
- ✅ Validation errors cleared when corrected
- ✅ Handles edge cases (empty, zero, negative, etc.)

---

#### Task 9: Implement "Clear All Data" Functionality
**Complexity:** ⭐ Low | **Est. Time:** 20 min  
**Deliverables:**
- Clear button handler
- Confirmation dialog
- Reset form to empty state
- Toast notification

**Code location:** `customCosts.clearAll()` function

```javascript
function clearAllData() {
  1. Show confirmation: "¿Borrar todos los datos ingresados?"
  2. If confirmed:
     - Remove all dynamically created rows
     - Clear input values
     - Reset RESUMEN totals to 0
     - Show toast: "Datos limpiados"
  3. If cancelled: do nothing
}
```

**Acceptance Criteria:**
- ✅ Confirmation prevents accidental data loss
- ✅ All rows deleted
- ✅ Totals reset to 0
- ✅ Form ready for new data

---

#### Task 10: Implement "Save Scenario" Dialog & Logic
**Complexity:** ⭐⭐⭐ High | **Est. Time:** 50 min  
**Deliverables:**
- Save button handler
- Modal dialog for scenario name input
- Auto-suggest name format
- Save to localStorage with metadata
- List management (show existing scenarios, delete old ones)
- Success notification and navigation

**Code location:** `customCosts.saveScenario()` and modal logic

```javascript
function saveScenario() {
  1. Validate all rows (use Task 8)
  2. If invalid: show toast errors, return
  3. If valid:
     - Show modal dialog
     - Auto-generate name: "Costos Reales - [CROP] [DATE] (v1)"
     - Allow user to edit name
     - Store with metadata:
       * id: "custom_soja_1713967200" (timestamp)
       * name: user input
       * crop: currentCrop
       * createdAt: ISO8601
       * lastModified: ISO8601
       * active: false (default)
       * totales: calculated values
     - Save to localStorage[getKey(crop)] as array
     - Show toast: "✓ Costos guardados exitosamente"
     - Close modal
     - (Optional) Show list of saved scenarios
}
```

**Acceptance Criteria:**
- ✅ Modal dialog appears with name input
- ✅ Default name suggested
- ✅ Can edit name before save
- ✅ Saves to localStorage correctly
- ✅ Can retrieve saved data after page refresh
- ✅ Multiple scenarios per crop are independent
- ✅ Success notification shown

---

### 🔗 PHASE 3: Integration & Polish

#### Task 11: Add Button to Existing "Ventana de Costos"
**Complexity:** ⭐ Low | **Est. Time:** 25 min  
**Deliverables:**
- Add "📋 Anotar Costos Personalizados" button to cost panel
- Button styling consistent with existing design
- Click handler navigates to custom costs view
- Pass current crop context

**Code location:** Modify existing HTML section for `ventanaCostos`

```html
<!-- Add button in existing cost panel header:
<button id="customCostsEditorBtn" class="btn secondary">
  📋 Anotar Costos Personalizados
</button>
```

**Acceptance Criteria:**
- ✅ Button visible in cost panel
- ✅ Button styling matches design
- ✅ Click navigates to custom costs view
- ✅ Current crop context passed correctly

---

#### Task 12: Implement Scenario Toggle (Estimated vs. Custom Costs)
**Complexity:** ⭐⭐⭐ High | **Est. Time:** 45 min  
**Deliverables:**
- Radio button or dropdown in cost panel
- Load saved custom costs scenarios for crop
- Toggle active scenario
- Persist toggle state in localStorage
- Visual indicator when custom costs active

**Code location:** Modify cost panel + `customCosts.setActiveScenario()`

```html
<!-- Add selector in cost panel:
<div class="scenario-selector">
  <label>
    <input type="radio" name="costScenario" value="estimated" checked />
    Usar costos estimados
  </label>
  <label>
    <input type="radio" name="costScenario" value="custom" />
    Usar costos personalizados
    <select id="customScenarioSelect">
      <option value="">-- Seleccionar --</option>
      <!-- Populated by JavaScript -->
    </select>
  </label>
</div>
```

**JavaScript logic:**
```javascript
// On toggle change:
1. Get selected scenario ID (if custom selected)
2. If custom: load scenario from localStorage
3. Call customCosts.setActiveScenario(crop, scenarioId)
4. Trigger rentabilidad recalculation (Task 13)
5. Show visual indicator: "✓ Usando costos personalizados"
6. Persist choice in localStorage
```

**Acceptance Criteria:**
- ✅ Can switch between estimated and custom
- ✅ Dropdown populated with saved scenarios
- ✅ Toggle persists after page refresh
- ✅ Visual feedback when custom active
- ✅ Can delete scenarios from dropdown

---

#### Task 13: Integrate Custom Costs into Rentabilidad Calculations
**Complexity:** ⭐⭐⭐ High | **Est. Time:** 60 min  
**Deliverables:**
- Modify existing rentabilidad calculation functions
- Use custom costs when active
- Recalculate all analysis panels:
  - Punto de equilibrio (break-even yield)
  - Bonificaciones (quality bonuses)
  - Cobertura de precios (price coverage)
  - Análisis de sensibilidad (sensitivity analysis)
- Preserve original calculations when using estimated costs

**Code location:** Modify existing rentabilidad functions in main HTML

```javascript
// At start of each rentabilidad function:
function calculateRentabilidad(crop) {
  // Determine which costs to use
  const activeCustom = customCosts.getActiveScenario(crop);
  const costData = activeCustom 
    ? activeCustom.totales  // custom costs
    : estimatedCosts[crop].totales;  // estimated costs
  
  // Use costData for all calculations:
  const gastosDirectos = costData.gastosDirectos;
  const totalGastos = costData.totalGastos;
  const puntoEquilibrio = totalGastos / precioNeto;
  
  // ... rest of calculations using costData
}
```

**Acceptance Criteria:**
- ✅ All calculation functions work with custom costs
- ✅ Punto de equilibrio recalculates correctly
- ✅ All analysis panels reflect new costs
- ✅ Switching between estimated/custom updates UI
- ✅ Charts and indicators update in real-time
- ✅ No errors in console

---

#### Task 14: Implement PDF Export for Custom Costs
**Complexity:** ⭐⭐⭐ High | **Est. Time:** 60 min  
**Deliverables:**
- Export button functionality
- PDF layout matching image format
- Two-column layout (insumos + labores)
- Includes totals and metadata
- Uses existing jsPDF + jsPDF-autotable libraries

**Code location:** `customCosts.exportPDF()` function

```javascript
function exportPDF() {
  1. Gather current form data (all rows, totals)
  2. Initialize jsPDF document
  3. Create table with structure:
     - Left column: INSUMOS (herbicidas, insecticidas, fertilizantes)
     - Right column: LABORES
     - Bottom: RESUMEN DE COSTOS
  4. Add metadata:
     - Crop name
     - Date exported
     - Scenario name
  5. Generate filename: "Costos_[Crop]_[Date].pdf"
  6. Download via browser
  7. Show toast: "✓ PDF generado exitosamente"
}
```

**Acceptance Criteria:**
- ✅ PDF layout matches image format
- ✅ All data included (insumos, labores, totals)
- ✅ Two-column layout or similar readable format
- ✅ File downloads with correct filename
- ✅ PDF is readable and prints well
- ✅ No jsPDF errors

---

#### Task 15: Testing, Refinement & Final Polish
**Complexity:** ⭐⭐⭐ High | **Est. Time:** 90 min  
**Deliverables:**
- Manual testing across browsers
- Edge case handling
- Performance optimization
- UX refinement
- Documentation updates
- Final code review

**Test scenarios:**
```
1. Add rows, delete rows, recalculate:
   ✓ Add 5 insumos, verify totals
   ✓ Delete row 3, verify totals recalculate
   ✓ Edit cantidad, verify cost updates live
   
2. Validation:
   ✓ Try to save with empty fields → shows errors
   ✓ Try to save with invalid numbers → shows errors
   ✓ Clear all data → confirmation required
   
3. Save & Persist:
   ✓ Save scenario → verify in localStorage
   ✓ Refresh page → scenario still there
   ✓ Save 3 scenarios → can select any
   
4. Integration:
   ✓ Toggle to custom costs → rentabilidad recalculates
   ✓ Change custom costs → all panels update
   ✓ Switch back to estimated → original values restored
   
5. Export:
   ✓ Generate PDF → file downloads
   ✓ PDF is readable and complete
   ✓ Multiple exports work without errors
   
6. Edge cases:
   ✓ Very large numbers (999999)
   ✓ Very small numbers (0.01)
   ✓ Rapid clicking (add/delete spam)
   ✓ Form submission with enter key
   ✓ Back button while editing (data preserved in UI)
```

**Acceptance Criteria:**
- ✅ All 6 test scenarios pass
- ✅ No console errors
- ✅ Works on Chrome, Firefox, Safari, Edge
- ✅ Mobile responsive (tested on mobile viewport)
- ✅ All toast notifications display correctly
- ✅ All modals dismiss properly
- ✅ Code is clean and well-commented

---

## Implementation Order & Dependencies

```
Phase 1 (Foundation)
├─ Task 1: localStorage schema ✓ (no deps)
├─ Task 2: Router entry ✓ (no deps, can run parallel with 1)
├─ Task 3: HTML markup ✓ (depends on 1)
├─ Task 4: CSS styling ✓ (depends on 3)
│
Phase 2 (UI & Editor)
├─ Task 5: Add rows ✓ (depends on 1, 3, 4)
├─ Task 6: Delete rows ✓ (depends on 5)
├─ Task 7: Real-time calc ✓ (depends on 5, 6)
├─ Task 8: Validation ✓ (depends on 7)
├─ Task 9: Clear data ✓ (depends on 6)
├─ Task 10: Save scenario ✓ (depends on 1, 8)
│
Phase 3 (Integration)
├─ Task 11: Add button ✓ (depends on 2, 10)
├─ Task 12: Toggle scenario ✓ (depends on 1, 10, 11)
├─ Task 13: Rentabilidad integration ✓ (depends on 12)
├─ Task 14: PDF export ✓ (depends on 10)
└─ Task 15: Testing & polish ✓ (depends on all)
```

---

## Estimated Total Time

- **Phase 1:** 135 minutes (2.25 hours)
- **Phase 2:** 215 minutes (3.6 hours)
- **Phase 3:** 285 minutes (4.75 hours)
- **Total:** ~595 minutes ≈ **10 hours** (realistic with debugging/refinement)

---

## Success Metrics

✅ **Functional:**
- User can add custom costs and save multiple scenarios
- Rentabilidad calculations use custom costs when active
- Data persists and survives page refresh

✅ **Quality:**
- No console errors
- All validations work
- PDF export matches design
- Mobile responsive

✅ **UX:**
- Smooth, no lag
- Clear error messages
- Intuitive navigation
- Fast save/load

---

## Next Steps

1. **Approval:** User confirms implementation plan is ready
2. **Development:** Execute tasks in order (1→15)
3. **Testing:** Run all test scenarios in Phase 3
4. **Deployment:** Merge to main, push to production
5. **Monitoring:** Watch for user feedback
