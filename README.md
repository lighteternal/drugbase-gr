# drugbase-gr

**A structured, open corpus of all authorized pharmaceutical products in Greece.**

Sourced from the Hellenic National Organization for Medicines (ΕΟΦ) and the European Medicines Agency (EMA). Covers every drug currently on the Greek market: patient information leaflets (ΦΟΧ), summaries of product characteristics (SmPC), and full drug registry metadata — all in Greek.

> Built to power free, open drug information search for patients, pharmacists, and researchers in Greece.

---

## Coverage

| Metric | Value |
|---|---|
| Registered drugs (EOF registry) | 15,522 |
| Drugs with at least one document | 15,177 (97.8%) |
| Unique brand names | 5,018 |
| Active substances | 2,103 |
| ATC codes | 1,468 |
| Patient leaflets (ΦΟΧ) | 5,810 |
| Summaries of product characteristics (SmPC) | 5,546 |
| Combined product information documents | 1,424 |
| Labelling documents | 665 |
| **Total documents** | **13,445** |

---

## Therapeutic categories (ATC level 1)

| ATC | Category | Brands |
|---|---|---|
| N | Nervous system | 684 |
| C | Cardiovascular system | 673 |
| L | Antineoplastic & immunomodulators | 672 |
| A | Alimentary tract & metabolism | 589 |
| J | Anti-infectives (systemic) | 586 |
| B | Blood & blood-forming organs | 413 |
| R | Respiratory system | 264 |
| G | Genito-urinary system & sex hormones | 229 |
| M | Musculo-skeletal system | 214 |
| S | Sensory organs | 212 |
| D | Dermatologicals | 192 |
| H | Systemic hormonal preparations | 156 |
| V | Various | 116 |
| P | Antiparasitic products | 13 |

---

## Top manufacturers (by number of authorized products)

| Manufacturer | Products |
|---|---|
| DEMO ABEE | 580 |
| RAFARM A.E.B.E. | 449 |
| ELPEN AE | 401 |
| ΒΙΑΝΕΞ Α.Ε. | 396 |
| BENNETT ΦΑΡΜΑΚΕΥΤΙΚΗ Α.Ε. | 290 |
| UNI-PHARMA ΑΒΕΕ | 278 |
| NOVARTIS EUROPHARM LIMITED | 273 |
| VOCATE ΦΑΡΜΑΚΕΥΤΙΚΗ Α.Ε. | 262 |
| ACCORD HEALTHCARE S.L.U. | 224 |
| PFIZER EUROPE MA EEIG | 179 |
| SANDOZ PHARMACEUTICALS D.D. | 160 |
| MYLAN PHARMACEUTICALS LIMITED | 149 |

The top 4 manufacturers are Greek domestic companies (DEMO, RAFARM, ELPEN, ΒΙΑΝΕΞ), collectively holding authorization for ~1,826 products — reflecting the strength of the Greek generics industry.

---

## Repository structure

```
drugbase-gr/
├── catalog.json          # Filterable index: one entry per brand
│                         #   fields: brand, substances, atc_codes, primary_atc,
│                         #           atc_level1/2, mahs, package_forms,
│                         #           has_pil, has_smpc, doc_count, sources
├── registry.jsonl        # Full drug registry (15,522 entries, one per package/SKU)
│                         #   fields: drug_id, brand_name, active_substances, atc_code,
│                         #           form, strength, packaging, mah, market_status,
│                         #           barcode, pricing, document_refs
├── brands_md/            # 5,018 Markdown files, one per brand
│                         #   Each file contains all PIL and SmPC sections
│                         #   in Greek, with YAML frontmatter for metadata
└── LICENSE               # CC BY 4.0
```

### `catalog.json` — machine-readable brand index

The primary entry point for building search and filter interfaces. Each entry:

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

One JSON line per authorized package/SKU. Cross-referenced with document IDs in `corpus.jsonl` (not included in this repo due to size — see below).

### `brands_md/` — human-readable drug leaflets

One Markdown file per brand, e.g. `brands_md/ELIQUIS.md`. Each file contains:

- YAML frontmatter: brand name, ATC, substances, MAH, package list, document count
- Package table: all authorized forms, strengths, and packaging variants
- Full text of each Patient Information Leaflet (ΦΟΧ) structured into sections 1–6
- Full text of each Summary of Product Characteristics (SmPC) structured into sections 1–6
- Source attribution (ΕΟΦ or EMA)

Section structure for PILs follows the standard EU format:
1. What it is and what it is used for
2. What you need to know before you take it
3. How to take it
4. Possible side effects
5. How to store it
6. Contents of the pack and other information

---

## What is NOT included

- `corpus.jsonl` (3.5 GB) — the full parsed document store with raw text and structured sections. Too large for git without LFS. Available on request or can be regenerated using the [pipeline repo](#pipeline).
- Documents for the 168 brands (3.3%) that have no publicly available PDF at time of crawl (mostly discontinued/withdrawn products).
- English-language EMA documents for products that have no Greek translation available.

---

## Data sources

| Source | Description |
|---|---|
| [ΕΟΦ eRegistry](https://www.eof.gr) | Official Greek national drug registry — all authorized products, barcodes, MAHs, ATC codes |
| [EMA Product Information](https://www.ema.europa.eu) | Centrally authorized products — Greek-language PILs and SmPCs from the EMA website |

All underlying documents are official public regulatory filings. This dataset adds structure: cross-referencing brands to documents, parsing PDFs into named sections, normalizing metadata.

---

## Usage examples

**Find all cardiovascular drugs by a Greek manufacturer:**
```python
import json

catalog = json.load(open("catalog.json"))
results = [
    b for b in catalog
    if b["atc_level1"] == "C"
    and any("DEMO" in m or "RAFARM" in m or "ELPEN" in m for m in b["mahs"])
]
print(f"{len(results)} cardiovascular drugs from Greek manufacturers")
```

**Search for all brands containing apixaban:**
```python
results = [b for b in catalog if "APIXABAN" in b["substances"]]
```

**Read a drug leaflet:**
```python
from pathlib import Path
text = Path("brands_md/ELIQUIS.md").read_text(encoding="utf-8")
```

---

## License

**CC BY 4.0** — Free to use, share, and adapt with attribution.

> **Cite as:** Papadopoulos, D. (2025). *drugbase-gr: A structured corpus of authorized pharmaceutical products in Greece*. GitHub. https://github.com/lighteternal/drugbase-gr

The underlying regulatory documents are public data from ΕΟΦ and EMA. This repository provides structured access to that data. See [LICENSE](LICENSE) for full terms.

---

## Related

- [legalize-gr](https://github.com/lighteternal/legalize-gr) — corpus of all Greek legislation, same approach applied to law
