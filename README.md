# Legado AI - UI Enhancement Implementation Guide



```
┌─────────────────────────────────────────────────────────────┐
│                    ENHANCED SYSTEM                           │
└─────────────────────────────────────────────────────────────┘

📤 User Uploads 18 PDFs
         ↓
┌─────────────────────────────────────┐
│  Document Processing (graph.py)     │
│  • OCR/Text Extraction             │
│  • Field Extraction (per doc)      │
│  • Store individual results         │
└─────────────────────────────────────┘
         ↓
┌─────────────────────────────────────┐
│  ✨ PHASE 1: Per-Document Review    │
│  (NEW!)                             │
│                                     │
│  📄 For EACH document:              │
│  • Show all 10 extracted fields    │
│  • Display confidence per field    │
│  • Allow user to edit/correct      │
│  • User confirms accuracy          │
│                                     │
│  ✅ User sees what was extracted!   │
└─────────────────────────────────────┘
         ↓
┌─────────────────────────────────────┐
│  Cross-Validation (backend)         │
│  • Use user-confirmed data         │
│  • 14 Coherence Rules              │
│  • Build comparison matrix         │
└─────────────────────────────────────┘
         ↓
┌─────────────────────────────────────┐
│  ✨ PHASE 2: Field Comparison       │
│  (NEW!)                             │
│                                     │
│  🔍 Side-by-side comparison:        │
│  • Show which doc has which value  │
│  • Highlight conflicts in color    │
│  • Explain each mismatch           │
│  • Provide remediation guidance    │
│                                     │
│  ✅ User understands conflicts!     │
└─────────────────────────────────────┘
         ↓
┌─────────────────────────────────────┐
│  ✨ PHASE 3: Final Analysis         │
│  (Enhanced)                         │
│                                     │
│  📊 Complete Summary:               │
│  • Traffic Light Status            │
│  • Remediation Plan                │
│  • PDF Report Download             │
│                                     │
│  ✅ User ready to take action!      │
└─────────────────────────────────────┘

Result: User confident and informed
```

---

## 🏗️ Three-Phase UI Architecture

### Overview

```
┌──────────────────────────────────────────────────────────────────┐
│                     USER JOURNEY                                  │
├──────────────────────────────────────────────────────────────────┤
│                                                                    │
│  PHASE 1              PHASE 2              PHASE 3               │
│  📄 Review           🔍 Compare            ✅ Confirm             │
│  Each Document       Across Docs          & Download             │
│                                                                    │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐          │
│  │ Deed     │        │ Owners   │        │ Traffic  │          │
│  │ Extract  │───────▶│ Deed: ✅ │───────▶│ Light:   │          │
│  │          │        │ Cad:  ✅ │        │ 🟡 YELLOW│          │
│  │ Edit ✏️  │        │ Reg:  ✅ │        │          │          │
│  └──────────┘        │          │        │ Download │          │
│                      │ Cadastral│        │ Report   │          │
│  ┌──────────┐        │ Deed: ✅ │        │ 📄 PDF   │          │
│  │ Cadastral│        │ Cad:  ✅ │        └──────────┘          │
│  │ Extract  │        │ Floor:❌ │                               │
│  │          │        │ EPC:  ✅ │                               │
│  │ Edit ✏️  │        │ CONFLICT!│                               │
│  └──────────┘        └──────────┘                               │
│                                                                    │
│  User builds         User identifies      User acts on           │
│  trust               conflicts            clear guidance         │
│                                                                    │
└──────────────────────────────────────────────────────────────────┘
```

### Phase 1: Per-Document Extraction Review

**Purpose:** Build user trust by showing what AI extracted from each document

**User View:**

```
┌─────────────────────────────────────────────────────────────────┐
│ 📄 Phase 1: Document Extraction Review                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│ ┌─── Tabs ───────────────────────────────────────────────────┐ │
│ │ [Deed] [Cadastral] [Floor Plan] [EPC] [Land Registry] ... │ │
│ └────────────────────────────────────────────────────────────┘ │
│                                                                   │
│ Currently Viewing: Deed_of_Provenance.pdf                       │
│                                                                   │
│ ┌───────────────────────────────────────────────────────────┐  │
│ │ Extracted Fields                                           │  │
│ ├───────────────────────────────────────────────────────────┤  │
│ │                                                            │  │
│ │ 👤 Property Owners               High 95%    [✏️ Edit]    │  │
│ │    First Name:  [Mario          ]                        │  │
│ │    Last Name:   [Rossi          ]                        │  │
│ │    Tax ID:      [RSSMRA75C15H501X]                       │  │
│ │    Share:       [50%            ]                        │  │
│ │    ─────────────────────────────────                      │  │
│ │    First Name:  [Laura          ]                        │  │
│ │    Last Name:   [Bianchi        ]                        │  │
│ │    Tax ID:      [BNCLRA80D45F205Y]                       │  │
│ │    Share:       [50%            ]                        │  │
│ │                                                            │  │
│ │ 🏛️ Right Type                    High 92%    [✏️ Edit]    │  │
│ │    [Full Ownership (Piena Proprietà)]                    │  │
│ │                                                            │  │
│ │ 🏘️ Cadastral IDs                Medium 78%   [✏️ Edit]    │  │
│ │    Sheet:      [23              ]                        │  │
│ │    Parcel:     [859             ]                        │  │
│ │    Subalterno: [18              ]                        │  │
│ │                                                            │  │
│ │ 📍 Address                       High 88%    [✏️ Edit]    │  │
│ │    Street:     [Via Roma 15     ]                        │  │
│ │    ZIP:        [20121           ]                        │  │
│ │    City:       [Milano          ]                        │  │
│ │                                                            │  │
│ │ 💰 Consideration Amount          High 96%    [✏️ Edit]    │  │
│ │    [€350,000                    ]                        │  │
│ │                                                            │  │
│ │ 📅 Date of Deed                  High 99%    [✏️ Edit]    │  │
│ │    [2023-06-15                  ]                        │  │
│ │                                                            │  │
│ │ 📋 Recording Date               Medium 85%   [✏️ Edit]    │  │
│ │    Date:    [2023-06-20         ]                        │  │
│ │    Number:  [REP 12345/2023     ]                        │  │
│ │                                                            │  │
│ │ ⚠️ Encumbrances                  Low 65%     [✏️ Edit]    │  │
│ │    [None (Libero da ipoteche)   ]                        │  │
│ │                                                            │  │
│ │ 🔒 Constraints                   Not Found   [✏️ Edit]    │  │
│ │    [Not mentioned in document   ]                        │  │
│ │                                                            │  │
│ │ [✅ Confirm This Document] [📝 Edit All Fields]          │  │
│ │                                                            │  │
│ └───────────────────────────────────────────────────────────┘  │
│                                                                   │
│ Progress: 1 of 18 documents reviewed                            │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- ✅ Tabbed interface for easy navigation between documents
- ✅ All 10 source-of-truth fields displayed
- ✅ Confidence indicator (High/Medium/Low + %)
- ✅ Color coding: Green (>90%), Yellow (70-90%), Red (<70%)
- ✅ Editable fields with inline correction
- ✅ Confirm button to approve extractions
- ✅ Progress tracker

### Phase 2: Field-by-Field Comparison Matrix

**Purpose:** Show exactly where data matches or conflicts across documents

**User View:**

```
┌─────────────────────────────────────────────────────────────────┐
│ 🔍 Phase 2: Cross-Document Field Comparison                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│ Priority Legend: [M] Mandatory · [P] Preferable · [S] Info     │
│                                                                   │
│ ═══════════════════════════════════════════════════════════════ │
│                                                                   │
│ 1️⃣ Property Owners [M]                                          │
│    Deed ↔ Cadastral Survey ↔ Land Registry                     │
│                                                                   │
│ ┌────────────┬─────────────┬─────────────┬─────────────────┐  │
│ │ Field      │ Deed        │ Cadastral   │ Land Registry   │  │
│ ├────────────┼─────────────┼─────────────┼─────────────────┤  │
│ │ Owner 1    │ Mario Rossi │ Mario Rossi │ Mario Rossi     │  │
│ │ Name       │     ✅      │     ✅      │      ✅         │  │
│ ├────────────┼─────────────┼─────────────┼─────────────────┤  │
│ │ Tax ID     │ RSSMRA75... │ RSSMRA75... │ RSSMRA75...     │  │
│ │            │     ✅      │     ✅      │      ✅         │  │
│ ├────────────┼─────────────┼─────────────┼─────────────────┤  │
│ │ Share      │ 50%         │ 50%         │ 50%             │  │
│ │            │     ✅      │     ✅      │      ✅         │  │
│ ├────────────┼─────────────┼─────────────┼─────────────────┤  │
│ │ Owner 2    │ Laura       │ L. Bianchi  │ Laura Bianchi   │  │
│ │ Name       │ Bianchi ✅  │     ⚠️      │      ✅         │  │
│ │            │             │ Name        │                 │  │
│ │            │             │ abbreviated │                 │  │
│ └────────────┴─────────────┴─────────────┴─────────────────┘  │
│                                                                   │
│ ✅ Status: CONDITIONAL                                          │
│ 📝 Issue: Minor name format difference in Cadastral Survey      │
│ 🔧 Action: Verify full name "Laura Bianchi" in cadastral records│
│ ⏱️ Timeline: 1-2 weeks                                          │
│ 💰 Estimated Cost: €100-200                                     │
│                                                                   │
│ ═══════════════════════════════════════════════════════════════ │
│                                                                   │
│ 2️⃣ Cadastral Identifiers [M]                                    │
│    Deed ↔ Cadastral ↔ Floor Plan ↔ EPC                         │
│                                                                   │
│ ┌─────────┬────────┬──────────┬────────────┬──────────────┐   │
│ │ Field   │ Deed   │ Cadastral│ Floor Plan │ EPC          │   │
│ ├─────────┼────────┼──────────┼────────────┼──────────────┤   │
│ │ Sheet   │ 23     │ 23       │ 23         │ 23           │   │
│ │         │  ✅    │   ✅     │    ✅      │    ✅        │   │
│ ├─────────┼────────┼──────────┼────────────┼──────────────┤   │
│ │ Parcel  │ 859    │ 859      │ 859        │ 859          │   │
│ │         │  ✅    │   ✅     │    ✅      │    ✅        │   │
│ ├─────────┼────────┼──────────┼────────────┼──────────────┤   │
│ │ Sub     │ 18     │ 18       │ 19         │ 18           │   │
│ │         │  ✅    │   ✅     │    ❌      │    ✅        │   │
│ │         │        │          │ CONFLICT!  │              │   │
│ │         │        │          │ Sub differs│              │   │
│ └─────────┴────────┴──────────┴────────────┴──────────────┘   │
│                                                                   │
│ ❌ Status: BLOCKING                                             │
│ 📝 Issue: Floor Plan shows subalterno 19, but 3 other docs show│
│          subalterno 18. This is a critical identifier mismatch. │
│ 🔧 Action: Update Floor Plan with correct subalterno (18) OR   │
│           verify if property actually has subalterno 19         │
│ ⏱️ Timeline: 2-3 weeks                                          │
│ 💰 Estimated Cost: €200-600 (cadastral office fees)            │
│ 🚫 Impact: BLOCKS CLOSING until resolved                        │
│                                                                   │
│ ═══════════════════════════════════════════════════════════════ │
│                                                                   │
│ 3️⃣ Property Address [M]                                         │
│    Deed ↔ Cadastral ↔ EPC                                       │
│                                                                   │
│ ┌──────────┬──────────────┬──────────────┬──────────────────┐ │
│ │ Field    │ Deed         │ Cadastral    │ EPC              │ │
│ ├──────────┼──────────────┼──────────────┼──────────────────┤ │
│ │ Street   │ Via Roma 15  │ Via Roma 15  │ Via Roma 15      │ │
│ │          │     ✅       │     ✅       │      ✅          │ │
│ ├──────────┼──────────────┼──────────────┼──────────────────┤ │
│ │ City     │ Milano       │ Milano       │ Milano           │ │
│ │          │     ✅       │     ✅       │      ✅          │ │
│ ├──────────┼──────────────┼──────────────┼──────────────────┤ │
│ │ ZIP      │ 20121        │ 20121        │ 20121            │ │
│ │          │     ✅       │     ✅       │      ✅          │ │
│ └──────────┴──────────────┴──────────────┴──────────────────┘ │
│                                                                   │
│ ✅ Status: MATCHING                                             │
│ 📝 All address fields are consistent across documents           │
│                                                                   │
│ ═══════════════════════════════════════════════════════════════ │
│                                                                   │
│ ... (7 more fields) ...                                         │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- ✅ Side-by-side comparison table format
- ✅ One column per document
- ✅ Visual status indicators (✅ ⚠️ ❌)
- ✅ Color coding: Green (matching), Yellow (conditional), Red (blocking)
- ✅ Specific conflict descriptions
- ✅ Clear remediation guidance with costs and timelines
- ✅ Priority tags [M], [P], [S]
- ✅ Impact assessment (blocks closing or not)

### Phase 3: Final Analysis & Download

**Purpose:** Provide comprehensive summary and downloadable report

**User View:**

```
┌─────────────────────────────────────────────────────────────────┐
│ ✅ Phase 3: Final Analysis & Report                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│ Overall Confidence: 87%                                          │
│                                                                   │
│ ┌───────────────────────────────────────────────────────────┐  │
│ │          🟡 CONDITIONAL STATUS                             │  │
│ │                                                             │  │
│ │  Property can close with remediation of identified issues  │  │
│ └───────────────────────────────────────────────────────────┘  │
│                                                                   │
│ ┌─── Summary ──────────────────────────────────────────────┐   │
│ │                                                            │   │
│ │ 📊 Documents Analyzed: 18                                 │   │
│ │ ✅ Fields Matching: 7 of 10                               │   │
│ │ ⚠️ Conditional Issues: 1                                  │   │
│ │ ❌ Blocking Issues: 1                                     │   │
│ │                                                            │   │
│ └───────────────────────────────────────────────────────────┘   │
│                                                                   │
│ ┌─── Required Actions ─────────────────────────────────────┐   │
│ │                                                            │   │
│ │ 🚨 CRITICAL (Must Fix Before Closing):                    │   │
│ │                                                            │   │
│ │ 1. Cadastral Identifier Mismatch                          │   │
│ │    • Floor Plan shows sub 19, should be 18               │   │
│ │    • Action: Update cadastral records                     │   │
│ │    • Timeline: 2-3 weeks                                  │   │
│ │    • Cost: €200-600                                       │   │
│ │                                                            │   │
│ │ ⚠️ RECOMMENDED (Should Fix):                              │   │
│ │                                                            │   │
│ │ 2. Name Format in Cadastral Survey                        │   │
│ │    • "L. Bianchi" should be "Laura Bianchi"              │   │
│ │    • Action: Request full name update                     │   │
│ │    • Timeline: 1-2 weeks                                  │   │
│ │    • Cost: €100-200                                       │   │
│ │                                                            │   │
│ └───────────────────────────────────────────────────────────┘   │
│                                                                   │
│ ┌─── Download Options ─────────────────────────────────────┐   │
│ │                                                            │   │
│ │ [📄 Download Full PDF Report]                             │   │
│ │ [📊 Export Comparison Matrix (Excel)]                     │   │
│ │ [📋 Download Remediation Plan]                            │   │
│ │                                                            │   │
│ └───────────────────────────────────────────────────────────┘   │
│                                                                   │
│ ┌─── Italian Summary ──────────────────────────────────────┐   │
│ │                                                            │   │
│ │ "L'analisi ha identificato 18 documenti con un livello    │   │
│ │  di coerenza dell'87%. È stato rilevato un conflitto      │   │
│ │  critico nell'identificativo catastale (subalterno 19 vs  │   │
│ │  18) che richiede correzione prima della chiusura. È      │   │
│ │  inoltre consigliata la verifica del nome completo nel    │   │
│ │  catasto. Tempo stimato di risoluzione: 2-3 settimane."   │   │
│ │                                                            │   │
│ └───────────────────────────────────────────────────────────┘   │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

**Key Features:**
- ✅ Traffic light status (Green/Yellow/Red)
- ✅ Overall confidence percentage
- ✅ Document count and field summary
- ✅ Prioritized action list (Critical vs Recommended)
- ✅ Timeline and cost estimates
- ✅ Multiple download options
- ✅ Italian language summary
- ✅ Clear next steps

---

