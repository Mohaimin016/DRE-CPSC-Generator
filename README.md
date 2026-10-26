# CPSC DRE Form Generator

A standalone GitHub Pages app for processing a DRE shipment report, checking cleaned HS/HTS codes against the embedded April 2026 CPSC HTS reference, screening commodity descriptions for child-related product terms, and generating reviewable CPSC PDF forms.

## Input mapping

The app detects columns by header name:

- `Tracking Number` → tracking number / output PDF filename
- `Shpr Company` → Manufacturer name and Submitter company name
- `Shipper Name` → Submitter name and signature text
- `Shpr Addr` → Manufacturer address
- `Shpr Phone` → Manufacturer phone and Submitter phone
- `MDE ORIG Commodity All` → Goods description (first 50 alphabetic words; standalone numeric tokens do not count toward the limit)
- `CE Item HSCode 1` → HS/HTS validation source

If `MDE ORIG Commodity All` is not present, the app can use `MDE3(DESC) Earliest` as fallback.

## Matching behavior

1. Split the HS-code field by its delimiters, clean each token, and ignore placeholders and entries shorter than 6 digits.
2. Exact matches against the embedded April 2026 CPSC HTS reference are shown as exact CPSC HTS matches and can generate forms.
3. Shorter HS-code prefixes with reference candidates are listed for review; a prefix alone is not treated as an exact classification.
4. The app also screens the full commodity description for child-related terms, including baby, infant, newborn, toy, pacifier, boy/boys, girl/girls, child/children, kid/kids, teen/teenage, toddler, nursery, stroller, crib, and related child-product terms.
5. A commodity-keyword hit can identify a shipment for CPSC review even when no exact CPSC HTS match exists. In that case, forms are labeled as keyword-only candidates, the source HS code is retained if available (otherwise the form's code field is blank), and the user must review the HTS code before submission. Keyword matching is a screening aid, not a legal product classification.
6. Forms are grouped by Tracking Number. The source goods field is capped at 50 alphabetic words. Because the supplied PDF has a single-line Description field, the text printed into that line is shortened with an ellipsis to fit the field, avoiding spillover into other form sections.

The embedded reference contains 590 HTS codes from the uploaded `CPSC_HTS_Codes_April_2026.xlsx` reference workbook. The PDF template is embedded in `index.html`.

## Deploy to a new GitHub repository

1. Create a **new** GitHub repository. This package does not modify the existing `CPSC-Checker` repository.
2. Upload `index.html` and `README.md` to the repository root.
3. Open **Settings → Pages**.
4. Choose **Deploy from a branch**, select branch `main` and folder `/ (root)`, then save.
5. Visit the Pages URL shown by GitHub.

## Notes

- This static app processes files in the browser; it does not upload the DRE report to server storage.
- It loads SheetJS, pdf-lib, and JSZip from public CDNs, so a network connection is needed.
- Commodity keyword hits are candidates for review and should not be considered an official determination that a product is subject to CPSC requirements.
