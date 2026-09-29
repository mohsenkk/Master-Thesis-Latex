---
name: thesis-paper-evidence
description: "Build a traceable evidence package from a specified research PDF for a specified thesis topic: located claims, visually verified LaTeX equations, architecture figures, and reuse-license evidence. Use for paper evidence extraction, not literature discovery or automatic chapter editing."
---

# Thesis Paper Evidence

Use when both a PDF and a thesis topic/subsection are specified. If either is missing, ask for it. Return evidence in the user's language; preserve original mathematical notation and figure identifiers.

## Workflow

1. Identify the actual PDF/version. Record its absolute path, SHA-256, title, DOI, and existing BibTeX key matched by DOI/title in the repository's `MyReferences.bib`. Compare metadata with the DOI record or publisher page; mark missing or conflicting fields, never invent a key. Distinguish PDF page indices (1-based) from printed page labels.
2. Extract text to locate relevant passages, then render and inspect the source pages. Record each claim as a concise paraphrase with section, column, paragraph/anchor and both page numbers; use a bounding box when helpful. Keep DTA regression distinct from binary DTI/DDI. Report only passages actually inspected and flag unreadable evidence rather than guessing.
3. For each selected equation, preserve its original number, signs, indices, conditioning, factors and any underbrace labels. Supply LaTeX and symbol definitions with their own source locators. Compare the transcription against the rendered original page, not just extracted text/OCR. Label the match verified, partial, or unreadable. Flag source inconsistencies separately; never silently correct, derive, or add an equation as article content. Record whether it is an objective to maximize/minimize or only one term.
4. For each relevant figure, record number, original caption (or explicitly labeled caption summary), page, and the thesis purpose. Extract only when requested/useful, retaining every panel, label, arrow, legend and caption; save crop coordinates and resolution. Visually compare the extraction with the complete source page for completeness, sharpness and English/Persian readability. If translation/redrawing is requested, verify it separately and identify it as a redraw with citation, never as the original.
5. Inspect the PDF copyright/license statement and publisher's article-specific license/reuse information; retain URLs and access date. Record original republication versus cited redraw, and whether permission is confirmed, required by an inspected notice, or unresolved. Access to a PDF or a citation alone does not establish reuse permission; do not infer a Creative Commons license or apply an author's exception to a third party. Report access failures without bypassing them.

Use available PDF text/render tools; extraction commands belong in a supporting file only if reusable detail is needed. Write packages/images to an explicit output folder, defaulting to a unique temporary folder. Do not modify chapters, presentations, bibliography, source PDFs, or other repositories.

## Evidence package

- Source manifest: topic, inspected pages, PDF path/hash/version, DOI, BibTeX key and matching status, metadata/license sources and access limitations.
- Claims table: ID, paraphrased claim, precise source locator, supported use and verification status.
- Equations: ID/number, source locator, LaTeX, symbol-definition locators, objective role and visual-match status; attach source-page/crop paths when produced.
- Figures table: number, caption/title, source locator, purpose, original/redraw designation, extraction path/bounds/resolution, visual QA and license/permission status.
- Keep three explicitly labeled categories: **Article text/formulas**, **Our interpretation**, **Proposed thesis method**. Put deductions or proposed extensions only in the latter two; mark the proposed-method category empty when no proposal was supplied.
- End with unresolved evidence, unreadable symbols/pages, extraction warnings and reuse limitations. Preserve PDF path, DOI, BibTeX key and both page numbers on evidence records, directly or through an explicit source ID. Deliver evidence only; no automatic prose/BibTeX insertion.
