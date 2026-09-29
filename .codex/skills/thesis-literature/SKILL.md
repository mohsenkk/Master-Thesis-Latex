---
name: thesis-literature
description: Find and evaluate review articles and original research for a named Chapter 2 subsection on drug-target binding affinity (DTA). Use for literature selection and evidence tables, not chapter drafting or bibliography editing.
---

# Thesis Literature

Given a subsection topic, return proposed sources and an evidence table in the user's language. Interpret the topic explicitly; a subsection number alone is not evidence of its current title.

## Research

- Read `MyReferences.bib` at the thesis root (three directories above this skill). Inventory accessible local PDFs before searching. For this project, also check `../../refferences` relative to the thesis root: the main papers are `final/GAN-vs-VAE-/DCGAN-DTA/paper.pdf` and `final/GAN-vs-VAE-/Co-VAE/papaer.pdf`. Verify paths at runtime; report missing or unreadable files. Do not modify the adjacent code repository.
- Search for both focused reviews and original papers relevant to the topic. Use publisher/DOI pages, author preprints and accessible PDFs; search snippets and existing BibTeX entries are leads, not verification. Deduplicate by DOI or identifier and distinguish preprint, online-first and journal versions.
- Check the task and target: regression of continuous binding affinity (DTA) differs from binary drug-target interaction (DTI), drug-drug interaction (DDI), toxicity and drug generation. Mixed reviews support this subsection only through their DTA material. Generic GAN/VAE papers support model foundations, not DTA performance claims.
- Match title, authors, year, venue and DOI/valid identifier against an actually opened publisher page or DOI metadata. Cross-check local PDF title pages. Record conflicts with the bibliography instead of silently accepting or correcting them. A preprint without a DOI may use its official repository identifier; do not invent a journal venue.
- Inspect the passages supporting each proposed claim. Record what was actually read: abstract, full-text HTML, or PDF with relevant page/section/table/figure. Access to a PDF or its title page alone is not a full-paper review. Identify secondary review claims versus primary experimental evidence. Describe evaluation data/splits when needed to bound a claim; do not infer comparable performance from different experiments.
- Mark each metadata field or claim that could not be checked as **unverified**, with the reason (blocked access, missing PDF, conflicting versions or insufficient text). Never invent sources, metadata, quotations or results. If access fails, try an available official/author alternative, then report the remaining gap without bypassing access controls.

## Output and scope

Return only proposed sources, the evidence table and brief access/conflict notes; no chapter prose or generated BibTeX. Do not edit thesis, presentation, bibliography or code files, install tools, or rebuild indexes as part of this skill.

For every proposed source include:

1. Title, authors, publication year and venue; DOI or valid identifier; link to the verification source; **review** or **original research** (and preprint status if applicable).
2. Its exact role in the subsection and the bounded claim it can support, with an evidence locator.
3. Inspection level and metadata-verification status, separately; existing BibTeX key and local PDF path when present.

Keep unsupported candidates separate from verified recommendations. Flag locally available DTI/DDI or unrelated papers that were excluded and explain why. Report missing access without treating it as proof that a paper or result does not exist.
