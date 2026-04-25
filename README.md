# drugbase-gr

> **A structured, open corpus of every authorized pharmaceutical product in Greece.**

Built from official regulatory filings of the Hellenic National Organization for Medicines (ΕΟΦ) and the European Medicines Agency (EMA). Covers the full Greek market: patient information leaflets (ΦΟΧ), summaries of product characteristics (SmPC), and complete drug registry metadata — all in Greek.

Designed to power free, open drug information search for patients, pharmacists, and researchers.

---

## At a glance

```
16,513 registry rows       ·    91.9% package document coverage · 5,474 brand/product pages
 2,290 active substances   ·    1,604 ATC codes                 · 13,445 documents
```

This snapshot contains **5,473 authorized medicine brands** plus **1 adjacent
metadata-only product** currently tracked to improve consumer-market coverage.

---

## Documents

| Type | Description | Count |
|---|---|---:|
| ΦΟΧ — Φύλλο Οδηγιών Χρήσης | Patient Information Leaflet | 5,810 |
| SmPC — Περίληψη Χαρακτηριστικών Προϊόντος | Summary of Product Characteristics | 5,546 |
| Product Information | Combined PIL + SmPC (EMA format) | 1,424 |
| Labelling | Package labelling text | 665 |
| **Total** | | **13,445** |

Each PIL is parsed into the 6 standard EU sections. Each SmPC into 14 structured subsections.

---

## Therapeutic categories

| ATC | Category | Brands | Share |
|---|---|---:|---:|
| N | Nervous system | 737 | 13.5% |
| C | Cardiovascular system | 698 | 12.8% |
| A | Alimentary tract & metabolism | 678 | 12.4% |
| L | Antineoplastic & immunomodulators | 672 | 12.3% |
| J | Anti-infectives (systemic) | 586 | 10.7% |
| B | Blood & blood-forming organs | 414 | 7.6% |
| R | Respiratory system | 374 | 6.8% |
| D | Dermatologicals | 277 | 5.1% |
| M | Musculo-skeletal system | 275 | 5.0% |
| G | Genito-urinary system & sex hormones | 234 | 4.3% |
| S | Sensory organs | 233 | 4.3% |
| H | Systemic hormonal preparations | 156 | 2.9% |
| V | Various | 117 | 2.1% |
| P | Antiparasitic products | 14 | 0.3% |

---

## Top manufacturers

| # | Manufacturer | Brand pages |
|---|---|---:|
| 1 | RAFARM A.E.B.E. | 185 |
| 2 | DEMO ABEE | 184 |
| 3 | UNI-PHARMA ΚΛΕΩΝ ΤΣΕΤΗΣ ΦΑΡΜΑΚΕΥΤΙΚΑ ΕΡΓΑΣΤΗΡΙΑ ΑΒΕΕ | 130 |
| 4 | ΒΙΑΝΕΞ Α.Ε. ΑΝΩΝΥΜΟΣ ΕΜΠΟΡΟΒΙΟΜΗΧΑΝΙΚΗ-ΤΟΥΡΙΣΤΙΚΗ-ΞΕΝΟΔΟΧΕΙΑΚΗ ΚΑΙ ΝΑΥΤΙΛΙΑΚΗ ΑΝΩΝΥΜΟΣ ΕΤΑΙΡΕΙΑ Δ.Τ. ΒΙΑΝΕΞ Α.Ε. | 126 |
| 5 | ELPEN AE ΦΑΡΜΑΚΕΥΤΙΚΗ ΒΙΟΜΗΧΑΝΙΑ | 124 |
| 6 | VERISFIELD ΜΟΝΟΠΡΟΣΩΠΗ Α.Ε. Δ.Τ. VERISFIELD | 100 |
| 7 | VOCATE ΦΑΡΜΑΚΕΥΤΙΚΗ Α.Ε. | 87 |
| 8 | ΚΟΠΕΡ Α.Ε. | 79 |
| 9 | BENNETT ΦΑΡΜΑΚΕΥΤΙΚΗ Α.Ε. | 78 |
| 10 | ΑΝΦΑΡΜ ΕΛΛΑΣ Α.Ε. | 71 |
| 11 | ΦΑΡΜΑΤΕΝ ΑΒΕΕ | 69 |
| 12 | NOVARTIS EUROPHARM LIMITED, IRELAND | 68 |

Manufacturer counts are grouped at brand-page level, not package/SKU level.

---

## Repository structure

```
drugbase-gr/
├── catalog.json       # Filterable index: one entry per brand/product (3.1 MB)
├── registry.jsonl     # Full registry: one line per package/SKU or adjacent product (22 MB)
├── brands_md/         # 5,474 Markdown files, one per brand/product (~906 MB total)
└── LICENSE            # CC BY 4.0
```

### `catalog.json` — brand index for search & filtering

The primary entry point for building search interfaces and filter UIs. One JSON object per brand:

```json
{
  "brand": "ELIQUIS",
  "md_file": "ELIQUIS.md",
  "substances": ["APIXABAN"],
  "atc_codes": ["B01AF02"],
  "primary_atc": "B01AF02",
  "atc_level1": "B",
  "atc_level2": "B01",
  "mahs": ["BRISTOL-MYERS SQUIBB/PFIZER EEIG, BELGIUM"],
  "package_forms": ["F.C.TAB 2,5MG/TAB", "F.C.TAB 5MG/TAB"],
  "drug_count": 4,
  "doc_count": 3,
  "has_pil": true,
  "has_smpc": true,
  "has_labelling": false,
  "sources": ["EMA"]
}
```

### `registry.jsonl` — drug registry

One JSON line per package/SKU or adjacent tracked product (16,513 total). Fields include `drug_id`, `brand_name`, `active_substances`, `atc_code`, `form`, `strength`, `packaging`, `mah`, `market_status`, `barcode`, `pricing`, and `document_refs` linking to the parsed document corpus when official documents are available.

### `brands_md/` — structured drug leaflets in Greek

One Markdown file per brand, e.g. `brands_md/ELIQUIS.md`. Each file contains:

- **YAML frontmatter** — brand name, ATC code, active substances, MAH, package count, document count
- **Package table** — all authorized pharmaceutical forms, strengths, and packaging variants with status
- **Patient Information Leaflets (ΦΟΧ)** — full Greek text structured into 6 standard EU sections:
  1. Τι είναι και ποια η χρήση του *(What it is and what it is used for)*
  2. Τι πρέπει να γνωρίζετε πριν το πάρετε *(What you need to know before taking it)*
  3. Πώς να το πάρετε *(How to take it)*
  4. Πιθανές ανεπιθύμητες ενέργειες *(Possible side effects)*
  5. Φύλαξη *(How to store it)*
  6. Περιεχόμενα συσκευασίας & λοιπές πληροφορίες *(Pack contents and other information)*
- **Summaries of Product Characteristics (SmPC)** — full Greek text structured into 14 subsections covering indications, posology, contraindications, warnings, interactions, pharmacology, and more
- **Source attribution** — ΕΟΦ or EMA

---

## Usage examples

**Filter by therapeutic area:**
```python
import json

catalog = json.load(open("catalog.json"))

# All oncology brands
oncology = [b for b in catalog if b["atc_level1"] == "L"]
print(f"{len(oncology)} antineoplastic brands")  # → 672
```

**Find all generics for a substance:**
```python
apixaban_brands = [b for b in catalog if "APIXABAN" in b["substances"]]
# → ELIQUIS, ABOXOMA, APIXABAN KRKA, APIXABAN SANDOZ, ...
```

**Find cardiovascular drugs from Greek manufacturers:**
```python
greek_cardio = [
    b for b in catalog
    if b["atc_level1"] == "C"
    and any("DEMO" in m or "RAFARM" in m or "ELPEN" in m for m in b["mahs"])
]
```

**Read a full drug leaflet:**
```python
from pathlib import Path
leaflet = Path("brands_md/ELIQUIS.md").read_text(encoding="utf-8")
```

**List all brands with both PIL and SmPC:**
```python
complete = [b for b in catalog if b["has_pil"] and b["has_smpc"]]
print(f"{len(complete)} brands with complete documentation")
```

---

## License

**[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)** — Free to use, share, and adapt for any purpose, including commercial, with attribution.

> **Cite as:** Papadopoulos, D. (2026). *drugbase-gr: A structured corpus of authorized pharmaceutical products in Greece*. GitHub. https://github.com/lighteternal/drugbase-gr

The underlying regulatory documents are official public filings from ΕΟΦ and EMA. This repository provides structured, machine-readable access to that data: cross-referenced, section-parsed, and normalized. See [LICENSE](LICENSE) for full terms.
