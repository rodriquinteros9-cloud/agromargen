# Custom Costs Editor - Design Specification
**Date:** 2026-04-24  
**Status:** Design Phase  
**Scope:** New feature for AGROMARGEN 2026/27

---

## 1. Overview

This feature enables agricultural clients to input and manage **actual, real-world production costs** for crops/sequences, storing them as reusable scenarios in localStorage. Clients can toggle between estimated costs (current system) and their custom costs for analysis and reporting.

**Key principle:** Custom costs are treated as a new type of "scenario" (similar to existing rentabilidad scenarios), allowing multiple versions to be saved and compared.

---

## 2. User Stories

### Story 1: Access Custom Cost Editor
**As a** crop analyst  
**I want to** access a dedicated editor for entering real production costs  
**So that** I can save accurate cost data linked to my farm's actual expenses

- Triggered by button in current "Ventana de Costos"
- Navigates to new view: `#costos-personalizados`
- Shows current crop/sequence context

### Story 2: Input Insumos (Inputs)
**As a** user  
**I want to** add/edit herbicides, insecticides, and fertilizers in separate sections  
**So that** I can record actual chemical and fertility costs

- Three subsections (Herbicidas, Insecticidas, Fertilizantes)
- Each row has: Name, Type, Quantity, Unit, USD Value, Cost (auto-calculated)
- "Add row" button for each subsection
- Delete individual rows with [×] icon

### Story 3: Input Labores (Labor Costs)
**As a** user  
**I want to** enter labor costs (seeding, spraying, harvesting, etc.)  
**So that** I capture the full picture of production labor expenses

- Fixed list of labor types (Siembra, Pulverización, Cosecha, P Aérea, etc.)
- Each row: Task name, Quantity, Price/unit, Total cost
- "Add row" button for custom labor types
- Delete rows with [×] icon

### Story 4: Auto-Calculate Totals
**As a** user  
**I want** totals to update automatically as I edit  
**So that** I always see accurate cost summaries without manual calculation

- Real-time calculation of:
  - Total insumos by category
  - Total labores
  - Total gastos directos
  - Total gastos (with alquiler + ad+estr)

### Story 5: Save Custom Costs Scenario
**As a** user  
**I want to** save my custom costs with a name and timestamp  
**So that** I can reuse it later or manage multiple versions

- Save dialog prompts for scenario name (auto-suggests format: "Costos Reales - [CROP] [DATE] (v1)")
- Stored in localStorage as new scenario type
- Shows success toast notification
- Can view list of saved custom costs scenarios

### Story 6: Toggle Active Scenario
**As a** user  
**I want to** choose whether analysis uses estimated or custom costs  
**So that** I can compare impact of real vs. estimated expenses

- Toggle in "Ventana de Costos" (radio buttons or dropdown)
- When custom costs active: cost panel shows "✓ Usando costos personalizados"
- All rentabilidad calculations recalculate with custom data
- Toggle is per-crop, persisted in localStorage

### Story 7: Export Custom Costs
**As a** user  
**I want to** export my custom costs to PDF  
**So that** I can share with agronomists or keep records

- Button in custom costs editor: "Exportar PDF"
- Output matches image format (two-column layout: insumos + labores)
- Includes crop name, date, totals

---

## 3. Data Model

### 3.1 Custom Costs Scenario Structure

```javascript
{
  // Metadata
  id: string,                    // unique: "custom_[crop]_[timestamp]"
  name: string,                  // "Costos Reales - SOJA 24/25 (v1)"
  crop: string,                  // "soja", "trigo", "maiz", etc.
  sequence: string,              // if applicable (e.g., "trigo_soja_2do")
  
  // Timestamps
  createdAt: ISO8601,            // when first saved
  lastModified: ISO8601,         // when last edited
  
  // Toggle for use in analysis
  active: boolean,               // is this scenario active for current crop?
  
  // Cost sections
  insumos: {
    herbicidas: Array<Insumo>,
    insecticidas: Array<Insumo>,
    fertilizantes: Array<Insumo>
  },
  labores: Array<Labor>,
  
  // Calculated totals (cached for UI)
  totales: {
    insumos: number,             // sum of all insumos costs
    labores: number,
    gastosDirectos: number,      // insumos + labores
    alquiler: number,            // pulled from estimados
    adEstr: number,              // pulled from estimados
    totalGastos: number          // gastosDirectos + alquiler + adEstr
  }
}

// Row objects
type Insumo = {
  id: string,                    // unique within scenario
  nombre: string,                // "Glifosato"
  tipo: string,                  // "Barbecho", "Pre-siembra", etc.
  cantidad: number,              // 1
  unidad: string,                // "L/ha", "kg/ha", etc.
  valorUSD: number,              // price per unit in USD
  costoUSD: number               // cantidad * valorUSD (auto-calc)
}

type Labor = {
  id: string,
  tarea: string,                 // "Siembra", "Pulverización", etc.
  cantidad: number,              // 3 (fumigations, e.g.)
  precioUnitario: number,        // 7.0
  costoUSD: number               // cantidad * precioUnitario (auto-calc)
}
```

### 3.2 Storage Location

- **Key in localStorage:** `agromargen_customCosts_[crop]`
- **Value:** Array of custom costs scenarios for that crop
- Survives page refresh, only cleared on explicit delete

### 3.3 Integration with Existing System

When user toggles "Use custom costs":
1. App reads `customCostsScenario.active = true` for current crop
2. In rentabilidad calculations, uses `customCostsScenario.totales` instead of `estimatedCosts.totales`
3. All analysis panels (punto de equilibrio, bonificaciones, etc.) recalculate with custom data

---

## 4. UI/UX Design

### 4.1 New View: Custom Costs Editor (`#costos-personalizados`)

#### Header
```
┌─────────────────────────────────────────────────────────┐
│ ← Volver  │  INGRESAR COSTOS PERSONALIZADOS            │
│           │  Cultivo: SOJA 24/25                        │
└─────────────────────────────────────────────────────────┘
```

#### Main Content (Three Sections)

**Section 1: INSUMOS**
```
┌────────────────────────────────────────────────────────────┐
│ INSUMOS - HERBICIDAS                        [+ Agregar fila]
├─────────────────────────────────────────────────────────────┤
│ │ Nombre    │ Tipo      │ Cant. │ USD/un │ Costo (USD) │ │
├─────────────────────────────────────────────────────────────┤
│ │ Glifosato │ Barbecho  │ 1     │ 5.8    │ 5.8         │ × │
│ │ [input]   │ [select]  │ [#]   │ [#]    │ [auto]      │ × │
└─────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│ INSUMOS - INSECTICIDAS                      [+ Agregar fila]
├─────────────────────────────────────────────────────────────┤
│ │ Nombre    │ Tipo        │ Cant. │ USD/un │ Costo (USD) │ │
├─────────────────────────────────────────────────────────────┤
│ │ Coragem   │ Insecticida │ 1     │ 180    │ 180         │ × │
│ │ [input]   │ [select]    │ [#]   │ [#]    │ [auto]      │ × │
└─────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│ INSUMOS - FERTILIZANTES                     [+ Agregar fila]
├─────────────────────────────────────────────────────────────┤
│ │ Nombre         │ Cant. │ USD/un │ Costo (USD) │ │
├─────────────────────────────────────────────────────────────┤
│ │ Urea           │ 120   │ 0.55   │ 66.0        │ × │
│ │ [input]        │ [#]   │ [#]    │ [auto]      │ × │
└────────────────────────────────────────────────────────────┘
```

**Section 2: LABORES**
```
┌────────────────────────────────────────────────────────────┐
│ LABORES                                     [+ Agregar fila]
├─────────────────────────────────────────────────────────────┤
│ │ Tarea       │ Cantidad │ Precio Unit. │ Costo (USD) │ │
├─────────────────────────────────────────────────────────────┤
│ │ Siembra     │ 1        │ 50.0         │ 50.0        │ × │
│ │ Fumigación  │ 5        │ 7.0          │ 35.0        │ × │
│ │ Cosecha     │ 1        │ 95.0         │ 95.0        │ × │
│ │ [input]     │ [#]      │ [#]          │ [auto]      │ × │
└────────────────────────────────────────────────────────────┘
```

**Section 3: RESUMEN (Read-only)**
```
┌────────────────────────────────────────────────────────────┐
│ RESUMEN DE COSTOS                                          │
├────────────────────────────────────────────────────────────┤
│ Total Insumos:         338.8 us$/ha                        │
│ Total Labores:         180.0 us$/ha                        │
│ ═════════════════════════════════════════════════════════ │
│ Total Gastos Directos: 518.8 us$/ha                        │
│ Alquiler:              383.5 us$/ha                        │
│ Ad + Estr:             50.0 us$/ha                         │
│ ═════════════════════════════════════════════════════════ │
│ TOTAL GASTOS:          952.3 us$/ha                        │
└────────────────────────────────────────────────────────────┘
```

#### Footer / Actions
```
┌────────────────────────────────────────────────────────────┐
│ [Limpiar]  [Exportar PDF]  [Guardar Costos Personalizados] │
└────────────────────────────────────────────────────────────┘
```

### 4.2 Integration with Existing "Ventana de Costos"

Add button to cost window:
```
┌────────────────────────────────────────┐
│ COSTOS ESTIMADOS                       │
│ [📋 Anotar Costos Personalizados]      │ ← NEW BUTTON
├────────────────────────────────────────┤
│ Selector de escenario:                 │
│ ○ Usar costos estimados                │
│ ● Usar costos personalizados           │
│  └─ "Costos Reales - SOJA v1" (v)     │ ← dropdown if multiple
│                                        │
│ [Cost breakdown...]                    │
└────────────────────────────────────────┘
```

---

## 5. Interaction & Behavior

### 5.1 Adding Rows
- Click "[+ Agregar fila]" button
- New blank row appears at bottom of section
- Focus on first input field (auto-focus for better UX)
- User fills in values
- Totals update in real-time as user types

### 5.2 Deleting Rows
- Click [×] icon on row
- Row is removed immediately
- Totals recalculate
- Optional: confirmation dialog for safety (recommended)

### 5.3 Validation
- **Nombre:** required, string
- **Cantidad:** required, positive number
- **USD values:** required, positive decimals (max 2 places)
- **Auto-calculated fields:** read-only, update on change
- Toast notification if validation fails

### 5.4 Save Dialog
When user clicks "Guardar Costos Personalizados":
1. Validate all rows (show errors if any)
2. If valid, show modal:
   ```
   ┌─────────────────────────────────────┐
   │ Guardar Costos Personalizados       │
   ├─────────────────────────────────────┤
   │ Nombre del escenario:               │
   │ ┌─────────────────────────────────┐ │
   │ │ Costos Reales - SOJA 24/25 (v1) │ │
   │ └─────────────────────────────────┘ │
   │                                     │
   │ [Cancelar] [Guardar]                │
   └─────────────────────────────────────┘
   ```
3. Save to localStorage with timestamp
4. Show success toast: "✓ Costos guardados exitosamente"
5. Optionally auto-return to "Ventana de Costos" or show list of saved scenarios

### 5.5 Clear Data
When user clicks "Limpiar":
1. Show confirmation: "¿Borrar todos los datos ingresados?"
2. If confirmed, clear all input rows (keep section headers)
3. Totals reset to 0
4. Toast: "Datos limpiados"

---

## 6. Technical Architecture

### 6.1 New Components

**File:** `customCostsEditor.html` (embedded in main HTML, show/hide via router)

**Key Functions:**
- `initCustomCostsView()` - Initialize view for selected crop
- `addInsumoRow(category)` - Add blank row to herbicida/insecticida/fertilizante
- `addLaborRow()` - Add blank row to labores
- `deleteRow(rowId, section)` - Remove row and recalculate
- `calculateTotals()` - Recalculate all totals in real-time
- `validateAndSave()` - Validate data, save to localStorage, show UI feedback
- `clearAllData()` - Clear form with confirmation
- `exportToPDF()` - Generate PDF export
- `loadCustomCosts(crop)` - Load previously saved custom costs for crop
- `toggleCustomCostsActive(crop, scenarioId)` - Switch between estimated/custom in analysis

### 6.2 Data Flow

```
User Input (table rows)
    ↓
Event listeners (input, change events)
    ↓
Calculate totals in real-time
    ↓
Update RESUMEN section
    ↓
User clicks "Guardar"
    ↓
Validate all rows
    ↓
Save to localStorage[agromargen_customCosts_[crop]]
    ↓
Show toast confirmation
    ↓
(Optional) Return to Ventana de Costos
```

### 6.3 Integration with Existing Rentabilidad Logic

When custom costs are active:
```javascript
// In existing rentabilidad calculation function:
const selectedScenario = getActiveCustomCosts(currentCrop);
const costData = selectedScenario ? selectedScenario.totales : estimatedCosts.totales;

// Use costData for all calculations:
// - Punto de equilibrio
// - Bonificaciones
// - Cobertura de precios
// - Análisis de sensibilidad
```

---

## 7. Success Criteria

- ✅ User can navigate to custom costs editor from any crop
- ✅ All insumo categories accept new rows dynamically
- ✅ Labores section accepts new rows
- ✅ Totals calculate and update in real-time
- ✅ Data persists in localStorage after refresh
- ✅ User can save multiple versions of custom costs per crop
- ✅ User can toggle between estimated and custom costs
- ✅ Rentabilidad analysis recalculates with custom data when active
- ✅ PDF export matches image format
- ✅ All forms validate before save

---

## 8. Out of Scope (for future)

- ☐ Supabase cloud sync for multi-device access (planned phase 2)
- ☐ Historical version comparison UI
- ☐ Bulk import from CSV
- ☐ Advanced unit conversion (always us$/ha)
- ☐ Real-time collaboration

---

## 9. Questions for Clarification

None at this time - design approved by client.
