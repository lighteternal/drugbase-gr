# drugbase-gr

> **A structured, open corpus of every authorized pharmaceutical product in Greece.**

Built from official regulatory filings of the Hellenic National Organization for Medicines (ΕΟΦ) and the European Medicines Agency (EMA). Covers the full Greek market: patient information leaflets (ΦΟΧ), summaries of product characteristics (SmPC), and complete drug registry metadata — all in Greek.

Designed to power free, open drug information search for patients, pharmacists, and researchers.

---

## At a glance

```
15,522 registered drugs    ·    97.8% document coverage    ·    5,018 brand names
 2,103 active substances   ·    1,468 ATC codes            ·   13,445 documents
```

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
| N | Nervous system | 684 | 13.6% |
| C | Cardiovascular system | 673 | 13.4% |
| L | Antineoplastic & immunomodulators | 672 | 13.4% |
| A | Alimentary tract & metabolism | 589 | 11.7% |
| J | Anti-infectives (systemic) | 586 | 11.7% |
| B | Blood & blood-forming organs | 413 | 8.2% |
| R | Respiratory system | 264 | 5.3% |
| G | Genito-urinary system & sex hormones | 229 | 4.6% |
| M | Musculo-skeletal system | 214 | 4.3% |
| S | Sensory organs | 212 | 4.2% |
| D | Dermatologicals | 192 | 3.8% |
| H | Systemic hormonal preparations | 156 | 3.1% |
| V | Various | 116 | 2.3% |
| P | Antiparasitic products | 13 | 0.3% |

---

## Top manufacturers

| # | Manufacturer | Country | Products |
|---|---|---|---:|
| 1 | DEMO ABEE | Greece | 580 |
| 2 | RAFARM A.E.B.E. | Greece | 449 |
| 3 | ELPEN AE | Greece | 401 |
| 4 | ΒΙΑΝΕΞ Α.Ε. | Greece | 396 |
| 5 | BENNETT ΦΑΡΜΑΚΕΥΤΙΚΗ Α.Ε. | Greece | 290 |
| 6 | UNI-PHARMA ΑΒΕΕ | Greece | 278 |
| 7 | NOVARTIS EUROPHARM LIMITED | Ireland | 273 |
| 8 | VOCATE ΦΑΡΜΑΚΕΥΤΙΚΗ Α.Ε. | Greece | 262 |
| 9 | ACCORD HEALTHCARE S.L.U. | Spain | 224 |
| 10 | PFIZER EUROPE MA EEIG | Belgium | 179 |
| 11 | SANDOZ PHARMACEUTICALS D.D. | Slovenia | 160 |
| 12 | MYLAN PHARMACEUTICALS LIMITED | Ireland | 149 |

The top 6 manufacturers are all Greek domestic companies, collectively holding authorization for over **2,400 products** — a reflection of the large and mature Greek generics industry. Greek manufacturers account for the majority of authorized SKUs on the market.

---

## Repository structure

```
drugbase-gr/
├── catalog.json       # Filterable index: one entry per brand (2.7 MB)
├── registry.jsonl     # Full drug registry: one line per package/SKU (20 MB)
├── brands_md/         # 5,018 Markdown files, one per brand (~906 MB total)
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

One JSON line per authorized package/SKU (15,522 total). Fields include `drug_id`, `brand_name`, `active_substances`, `atc_code`, `form`, `strength`, `packaging`, `mah`, `market_status`, `barcode`, `pricing`, and `document_refs` linking to the parsed document corpus.

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
