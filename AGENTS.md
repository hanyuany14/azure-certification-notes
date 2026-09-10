# AI-901 study knowledge base

Maintain this repository for **Exam AI-901: Microsoft Azure AI Fundamentals**. This file contains operating rules and the repository map; study content belongs in module notes.

## 1. Source authority
- Validate facts in this order: (1) current official Microsoft Learn documentation, (2) current Microsoft AI-901 Study Guide, (3) Microsoft product/SDK documentation, (4) original notes/screenshots, (5) practice questions.
- The [current English AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) governs exam scope. Always prioritize the latest English Microsoft Learn pages; the exam will be taken in English.
- Notes and practice questions never override current Microsoft documentation. Never silently preserve incorrect information in maintained notes; explicitly explain corrections while preserving originals.

## 2. Current vs legacy
- Label relevant content: **Current**, **Preview**, **Deprecated**, **Retiring**, **Retired**, or **Legacy exam-bank context**. Verify status rather than inferring it from age or naming.
- Explain differences between current names and older Azure Cognitive Services, Azure AI Services, Computer Vision, LUIS, and Bot Framework terminology; never mix them without explanation.
- Preserve useful legacy knowledge and old-question context in the module's **Current vs Legacy** section, explaining present status. Do not delete useful knowledge merely because it is old.

## 3. Exam scope
- Recheck the current English skills measured when changing scope or prioritization. Baseline verified 2026-09-05 (skills effective April 15, 2026): **Identify AI concepts and capabilities: 40–45%**; **Implement AI solutions by using Microsoft Foundry: 55–60%**. Update this baseline when Microsoft changes it.
- Prioritize responsible AI; generative model concepts/configurations; model selection/deployment; Microsoft Foundry; prompts; generative apps; agents; Foundry SDK; text analysis/Azure Language in Foundry Tools; speech recognition/synthesis/Azure Speech in Foundry Tools; multimodal audio and vision; computer vision; image generation; Azure Vision; Azure Content Understanding; extraction from documents, images, audio, and video; relevant REST API, SDK, and Python patterns.
- For original material no longer central to the skills measured, retain useful exam context and classify relevance as **Medium**, **Low**, **Legacy**, or **Adjacent**. Keep relevance distinct from product lifecycle status.

## 4. Study style
- Optimize for studying, not documentation completeness. Use structured/comparison tables and concise but detailed explanations; avoid long essays.
- Prefer **English concept → Traditional Chinese explanation → example → exam distinction**. Preserve official English names and exam keywords.
- Include realistic examples, input/output distinctions, relevant API/SDK patterns, common confusions, and short memory rules.

## 5. Markdown granularity and repository map
- Use **one Markdown file per major service/module**, with headings for concepts. Never create one file per concept or mistake question unless explicitly requested.
- Current layout:
  - `README.md`: public repository entry point for all certification notes.
  - `HACKMD_SYNC.md`: instructions for linking public notes to HackMD through GitHub Sync.
  - `ai-901/README.md`: AI-901 module overview.
  - `ai-901/knowledge/00_Index.md`: quick navigation for all AI-901 knowledge modules and their integrated mistake sections.
  - `ai-901/knowledge/01_Exam_Guide.md`: exam process, preparation strategy, resource order, and clearly labeled personal exam experience.
  - `ai-901/source/`: immutable original exports, PDFs, and screenshots.
  - `ai-901/knowledge/02_Responsible_AI.md`, `03_AI_Models_and_Workloads.md`, `04_Microsoft_Foundry.md`, `05_Text_and_Language.md`, `06_Speech.md`, `07_Computer_Vision.md`, `08_Content_Understanding.md`, `09_Legacy_and_Adjacent_Topics.md`: the eight canonical module notes.
  - `ai-901/assets/knowledge/`: study copies of useful knowledge images. `ai-901/assets/mistakes/`: every original question screenshot, preserved one-for-one.
- Keep prompts, deployment, and Foundry SDK/client patterns within the Foundry module; include service-specific code patterns in their service modules.
- Within Computer Vision, group Image Classification, Object Detection, OCR, Image Analysis, Azure Face, Domain-specific Models, multimodal vision, image generation, Current vs Legacy, and Common Confusions under headings, with verified scope/status labels.

## 6. Images
- Never discard original screenshots or diagrams. Preserve originals and maintain study copies in `ai-901/assets/`; use relative Markdown image links near the relevant knowledge section or mistake question.
- Preserve outdated/incorrect images and add an explicit correction immediately underneath. Keep provenance and avoid overwriting distinct images with the same export filename.

## 7. Mistake notes
- Place each module's questions in a **Common Mistakes and Detailed Explanations** section near the bottom of its canonical knowledge file. Do not create separate mistake Markdown files.
- Each question must include: original screenshot, tested concept, correct answer, detailed explanation, why every other option is wrong, question clue/keyword, current Microsoft terminology, official source, and short exam takeaway.
- Explain legacy expected answers separately from current behavior. Mark missing evidence or unverified answers **NEEDS VERIFICATION** rather than inventing them.

## 8. Verification
- For every factual change, verify against current Microsoft documentation, prefer English Microsoft Learn, preserve exact source links, and record meaningful current/legacy differences.
- Record verification dates for time-sensitive claims. Never fabricate URLs or citations; mark facts that cannot be confidently verified **NEEDS VERIFICATION**.

## 9. Original files
- Never modify files inside `ai-901/source/`; treat it as immutable source material. Create all cleaned/reorganized notes outside it.
- Existing exports also remain in `ai-901/raw notes/`; treat that directory as immutable. Originals have been copied into `ai-901/source/` with their structure and linked assets preserved.
- Migration and factual refinement are authorized for `ai-901/knowledge/`, `ai-901/assets/`, README files, and this repository map. Never rewrite the preserved originals.

## 10. Maintenance behavior
- Before adding content: identify its existing module, check whether the concept already exists, update that module instead of adding unnecessary files, avoid duplicate explanations, and update affected comparison tables.
- Use cross-links for shared concepts. Keep the exam map and navigation aligned with module changes; keep this file concise and free of study content.
