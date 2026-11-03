# TinyPaper · Vietnamese TinyLLM on ESP32-S3

![Static website](https://img.shields.io/badge/site-static_GitHub_Pages-365f9c)
![Paper](https://img.shields.io/badge/document-preprint_not_peer_reviewed-f2a45d)
![Website license](https://img.shields.io/badge/website-CC_BY--SA_4.0-3f917d)

Academic research page about **Vietnamese Tiny Language Models (TinyLLM) on ESP32-S3 microcontrollers**, inspired by the structure of [DeFM](https://de-fm.github.io/).

**Website:** https://xulytiengviet.github.io/Tinypaper/ (available after enabling GitHub Pages)  
**Preprint:** [PAPER.md](PAPER.md)  
**Author credit (to verify before submission):** Dai-Long Ngo-Hoàng (Long Ngo).  
**Status:** proposal / critical narrative review, data frozen 2026-11-03. **Not** an experimentally validated new Vietnamese MCU language model.

## Features

| Section | Function |
|---|---|
| Overview | Abstract, highlights, scope, and evidence limitations |
| Explorer | Interactive *ideal weight storage* estimator: 260K–45M parameters, FP16/INT8/INT4/2-bit/ternary |
| Method | T0–T3 offline-first architecture and VN-MCU-LM reproducibility agenda |
| Semantics | Vietnamese diacritics, word/syllable distinctions, semantic equivalence and safe action gating |
| Benchmarks | Four externally reported project measurements with direct links; **not** a controlled ranking |
| TinyLLM Models | Browsable third-party implementations with filter; explicit status of unreleased Vietnamese checkpoint |
| Images | Original inline SVG scientific illustrations |
| Demo | Local deterministic intent parser producing JSON; **not** a neural model and does **not** control GPIO |
| Cite | Copyable BibTeX, reference links, acknowledgments and CC BY-SA attribution |

Top links include **Paper, arXiv, Code, 🤗 Models, Images, Demo**. The arXiv badge is intentionally disabled because there is currently no verified submission ID. Model references point to independent public projects; this repository does not host their weights.

## GitHub Pages deployment (no build step)

1. Open **Settings → Pages** in this repository.
2. Under *Build and deployment*, select **Deploy from a branch**.
3. Choose **main** and **/(root)**, then Save.
4. Open https://xulytiengviet.github.io/Tinypaper/.
5. Check the **Actions** tab for a Pages deployment run if the site is not visible.

The homepage is a self-contained **index.html** (HTML/CSS/JavaScript), no server, Node.js, API key or database necessary. It works without installing an application. Fonts load from Google Fonts when Internet is present, otherwise system font fallbacks apply.

### Local preview

Open index.html in a browser, or serve it with:

~~~bash
python -m http.server 8000
~~~

Then visit http://localhost:8000.

## Scientific integrity

- The repository contains a **research website and preprint**, not a trained TinyLLM checkpoint.
- It reports reference numbers from external GitHub projects; they vary in task, software and test hardware.
- The *Explorer* only calculates theoretical model-weight storage. Real capacity additionally depends on KV cache, scales, runtime, stack/heap, flash access and PSRAM.
- The *Demo* is a transparent deterministic JavaScript baseline, not AI inference, not a device controller, and contains no server calls.
- A real comparison must version-control board specifications, exact prompts, input/output sequence lengths, model checksums, runs, latency percentiles and energy readings.

## Acknowledgments

Dự án CVNSS4.0 được nhà nghiên cứu **Trần Tư Bình** tài trợ, theo thông tin do nhóm dự án cung cấp.

The site acknowledges the academic webpage lineage: [Academic Project Page Template](https://github.com/eliahuhorwitz/Academic-project-page-template), [Nerfies](https://nerfies.github.io/) and [DeFM](https://de-fm.github.io/).

## License

The original website text, inline diagrams and website interface are provided under [Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/); see [LICENSE.md](LICENSE.md).

**Third-party software, research papers, model weights and datasets are not relicensed by TinyPaper.** Consult each source's own terms. This page uses original HTML/CSS/SVG implementation rather than copying third-party media.

## Suggested next research steps

Create and release a curated Vietnamese intent dataset with governance notes, implement a reproducible host baseline, build one ESP32-S3 inference target, publish ablation and hardware measurements, and only then declare VN-MCU-LM benchmark results.