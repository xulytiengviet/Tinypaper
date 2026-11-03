# TinyLLM tiếng Việt trên vi điều khiển ESP32-S3: Thực trạng, giới hạn và tiềm năng

**Vietnamese Tiny Language Models on ESP32-S3 Microcontrollers: Evidence, Feasibility Limits and a Reproducible Research Agenda**

> **Preprint · ngày khóa tư liệu: 03/11/2026 · chưa qua bình duyệt · chưa báo cáo kết quả thực nghiệm gốc.**

**Tác giả (theo thông tin dự án):** Dai-Long Ngo-Hoàng (Long Ngo)  
**Website:** https://xulytiengviet.github.io/Tinypaper/  
**Mã nguồn website:** https://github.com/xulytiengviet/Tinypaper  
**arXiv/DOI:** Chưa được cấp; không trích dẫn mã bài báo chưa tồn tại.

## Tóm tắt

Việc triển khai mô hình sinh ngôn ngữ lên vi điều khiển tài nguyên thấp như ESP32-S3 đang nhận được sự chú ý nhờ lượng tử hóa, tối ưu bộ nhớ nhiều cấp và các kiến trúc ngôn ngữ chuyên tác vụ. Tuy nhiên, cụm từ *TinyLLM* được sử dụng không đồng nhất, bao gồm cả mô hình phân loại ý định, mạng nhận dạng tiếng nói, Transformer sinh văn bản và hệ thống sử dụng MCU đơn thuần làm thiết bị đầu cuối cho LLM trên máy chủ. Bài báo đưa ra tổng quan phê bình các nguồn học thuật và mã nguồn mở có thể kiểm chứng đến ngày 03/11/2026, phân biệt suy luận trên chip với edge/cloud-assisted, đồng thời đặt giới hạn đối với cách suy diễn từ demo sang hệ thống hội thoại đa dụng. Các ví dụ TinyStories 260K, PLE 28,9M và Needle 2 45M cho thấy một số năng lực sinh ngôn ngữ hoặc gọi công cụ có thể vận hành cục bộ trên ESP32-S3, song khác nhau về chất lượng và độ trễ. Bài báo đề xuất kiến trúc TinyLLM tiếng Việt *offline-first* và bộ kiểm thử **VN-MCU-LM** nhằm đánh giá ngôn ngữ, công cụ, độ trễ, năng lượng, an toàn và tính tái lập. Đây là chương trình nghiên cứu có giả thuyết kiểm chứng, không phải khẳng định đã huấn luyện thành công mô hình hội thoại tiếng Việt chạy trên MCU.

**Từ khóa:** TinyLLM, TinyML, Vietnamese NLP, ESP32-S3, quantization, offline AI, benchmarking, safe IoT.

## Abstract

Language generation on ESP32-S3-class microcontrollers is becoming technically plausible through low-bit quantization, memory-mapped model storage and task-specific architectures. However, the term “TinyLLM” often conflates on-device autoregressive generation, constrained intent parsing, speech recognition and microcontrollers that merely forward data to remote models. This critical narrative review separates these deployment and task categories, evaluates publicly documented engineering demonstrations and proposes a Vietnamese-aware, offline-first architecture with a reproducible benchmark called VN-MCU-LM. Reported results from 260K, 28.9M and 45M parameter projects show that narrow generation or tool-calling can run locally, but do not demonstrate general-purpose Vietnamese conversation. We propose testable hypotheses on tokenization, constrained decoding, memory hierarchy and ternary quantization. No original device-level measurements are claimed.

## 1. Research questions

- **RQ1:** Which language capabilities have actually been demonstrated entirely on ESP32-S3?
- **RQ2:** How do memory, flash bandwidth, runtime and Vietnamese linguistic structure constrain the design?
- **RQ3:** What protocol enables reproducible comparison of quality, reliability, latency, memory and energy?
- **RQ4:** Which experimentally falsifiable improvements could justify a novel research contribution?

## 2. Method and evidence taxonomy

This is a structured **critical narrative review**, not a PRISMA systematic review or a quantitative meta-analysis. The source selection spans hardware documentation, peer-reviewed studies, preprints and GitHub repositories with technical details. Evidence is categorized independently by:

1. **Deployment:** A fully on-MCU inference; B local host/edge-assisted; C cloud-assisted.
2. **Task:** wake-word / KWS; ASR; classification; language generation; constrained tool calling; retrieval; vision.
3. **Evidence:** peer-reviewed results; preprint; reproducible repository artifacts; unverified self-report.
4. **Measurement:** board configuration, memory placement, model version, input/output context and instrumented latency.

A GitHub README can establish a public engineering claim, but should not automatically be treated as a peer-reviewed or independently reproduced result. In particular, a speech recognizer is not an autoregressive conversational LLM, and a vision Transformer is not an on-MCU language assistant.

## 3. State of the field

### 3.1. On-device generative language

**DaveBben / esp32-llm** ports the llama2.c family to ESP32-S3 and reports a 260K-parameter TinyStories checkpoint generating approximately **19.13 tokens/s** with dual-core and ESP-DSP optimizations. This is a small English-language story generator, not a robust instruction-following assistant. [R2]

**slvDev / esp32-ai** reports a **28.9M stored-parameter** TinyStories PLE model at **9.88 tokens/s**, with most of the large embedding table stored in flash. Crucially, the project explicitly warns that this model does not answer factual questions, follow instructions or write code. Parameter count alone is not a substitute for usable reasoning capability. [R3]

**Bellman281 / esp32-tinyLLM** ports the PLE inference engine to Rust and reports **10.50 tokens/s** under the default measured configuration compared with **8.20 tokens/s** for its local C reference. These figures refer to the author's paired comparison and should not be interpreted as a controlled comparison with all external implementations. [R4]

**Needle 2 / ESP32-S3** reports a 45M-parameter constrained JSON tool-calling model running without network access on a specified N16R8-class configuration. The published board measurements report **1.87 tokens/s**, with requests taking approximately **25 seconds without reasoning** or **47 seconds with reasoning**. It is a specialized dispatcher rather than an open-ended chatbot. [R5]

### 3.2. Related, but not interchangeable, results

ASR such as Oído and Vietnamese KWS demonstrate speech-processing feasibility on MCUs, but do not on their own demonstrate a conversational language model. Vision Transformer results like FTerViT demonstrate ternary and Transformer-related efficiency, but address a different modality. MCU clusters and heterogeneous RISC-V accelerators help explore distribution and mapping, yet cannot be represented as verified performance on a single stock ESP32-S3. [R8–R10]

### 3.3. Implications for Vietnamese

Vietnamese uses tonal diacritics, frequently contains words comprising multiple space-separated syllables, and is commonly typed without diacritics in informal interfaces. Those characteristics require explicit experimental control of Unicode normalization (NFC/NFD), slang, abbreviations, regional variants, homophones, code-switching and ambiguous commands. PhoBERT supplies an example of Vietnamese-language pretraining, but its scale and use case are not equivalent to on-MCU generation. [R7]

## 4. Memory feasibility

For a model with **P** parameters stored using an average of **b** bits/parameter, the ideal weight-only storage is:

\[
M_{\rm weight}\approx \frac{P \times b}{8}\text{ bytes}.
\]

This bound excludes activation memory, KV cache, quantization scales, tokenizer, runtime and firmware, stacks, fragmentation and alignment. On a decoder with **L** layers, **T** context tokens, key/value width **d_{kv}** and cache bit width **b_{kv}**, a simplified bound is:

\[
M_{\rm KV}\approx \frac{2LTd_{kv}b_{kv}}{8}.
\]

| Parameter count | FP16 weight-only minimum | Q4 weight-only minimum |
|---:|---:|---:|
| 0.26M | 0.52 MB | 0.13 MB |
| 3M | 6.00 MB | 1.50 MB |
| 8M | 16.00 MB | 4.00 MB |
| 28.9M | 57.80 MB | 14.45 MB |
| 45M | 90.00 MB | 22.50 MB |

Values in the table use **decimal MB**, not MiB. A model that fits into external flash does **not** necessarily fit the working RAM budget. The memory model and the layout actually used by PLE, structured quantizers or mixed-precision checkpoints can differ substantially from a uniform-Q4 approximation.

### 4.1. Ternary representations

With three equiprobable weights from \(\{-1,0,+1\}\), the information-theoretic ideal is \(\log_2 3 \approx 1.585\) bits/weight. Real implementations depend on block packing, scaling constants, metadata and kernel behavior. A symbolic alphabet of 27 elements may be mapped bijectively to three balanced trits, since \(3^3=27\), but this identity **does not establish** better Vietnamese text compression or accuracy. Such benefits require end-to-end experiments with a fully specified coding scheme and equal experimental budgets.

## 5. Vietnamese TinyLLM architecture

An **offline-first, capability-tiered system** is proposed:

- **T0 · Deterministic preprocessing:** Unicode normalization, constrained vocabulary checks, simple safety rules and reproducible baseline.
- **T1 · Signal TinyML:** optional wake word, KWS or sensor classification, reported as a separate subsystem.
- **T2 · Specialized TinyLM:** Vietnamese intent parsing or limited text generation, producing grammar-constrained tool calls.
- **T3 · Optional host/cloud fallback:** only when allowed by the user, with clear disclosure that inference is not wholly on the MCU.

A runtime safety shield validates grammar, parameters, access control, current device state and refusal policy. For safety-critical actions, remote or physical human confirmation is required; the language model must never be treated as a sufficient authorization mechanism.

## 6. VN-MCU-LM proposed benchmark

Six evaluation strata are defined:

| Stratum | Scope | Example metrics |
|---|---|---|
| D1 | Vietnamese with canonical diacritics | intent macro-F1; slot exact match |
| D2 | No-diacritic, malformed, mixed Unicode | robust accuracy; edit invariance |
| D3 | Paraphrases, dialects and informal speech | paraphrase consistency |
| D4 | State-dependent multi-condition commands | action success; p95 latency |
| D5 | Unauthorized, ambiguous, out-of-scope input | unsafe-action rate; correct refusal |
| D6 | Short generation and broader language probes | task-specific score; hallucination rate |

Hardware reporting should include exact board name, flash/PSRAM configuration, CPU clock, ESP-IDF build, test corpus SHA, inference engine commit, tokenizer/model checksum, prompt length distribution and output lengths. Compare like-for-like model settings, record p50/p95 end-to-end latency, tokens/s where applicable, peak SRAM/PSRAM, joules/request, board stability and offline completion rate.

**No VN-MCU-LM measurements are published by this site at this stage.** Public GitHub throughput numbers are shown solely for an illustrative cross-project overview, never as a controlled ranking.

## 7. Falsifiable hypotheses

- **H1 / Tokenizer:** Vietnamese-adapted tokenization changes sequence length and/or intent accuracy under a fixed parameter and training budget.
- **H2 / Constrained output:** grammar-constrained decoding plus a runtime safety shield reduces invalid or unsafe tool actions compared with unconstrained generation.
- **H3 / Hierarchical memory:** flash/PSRAM/SRAM placement expands feasible checkpoint storage while imposing measurable bandwidth and latency costs.
- **H4 / Ternary:** ternary-aware kernels improve the storage/energy frontier for selected architectures without presuming a universal win.
- **H5 / Hybrid:** a rule/TinyML/TinyLM architecture improves practical command success per joule and latency relative to an autoregressive-only MCU baseline on the same tasks.

For each hypothesis, the research team should publish a baseline, independent test set, equipment, repeat count, pre-registered success condition and failure conditions.

## 8. Proposed experimental roadmap

| Phase | Focus | Exit criteria (proposed, not measured results) |
|---|---|---|
| 0–2 months | Dataset governance; Vietnamese baselines; train/validation/test split | documented corpus, label audit, reproducible baseline |
| 2–5 months | Tokenizer + model training + quantization | model-card draft, controlled ablations, host-level parity |
| 5–8 months | ESP32-S3 runtime integration | tested firmware, memory trace, board measurements |
| 8–12 months | Statistical evaluation and reproducibility | confidence intervals, source code, data statement, paper revision |

These are planning estimates only, not verified training durations.

## 9. Limitations and research ethics

No unique, broadly agreed definition of “TinyLLM” exists. Cross-repository results are heterogeneous, sometimes lack controlled measurement, and are prone to survivorship and reporting bias. Source code and preprints should be tracked by commit and publication date. Vietnamese conversational competence is not proven by narrow TinyStories generation or keyword recognition. Demo safety classifiers should not be deployed as security controls without additional verification. User transcripts must not be transmitted or retained without disclosure and informed permission. Copyright, model licenses and dataset provenance require separate audit.

## 10. Conclusion

ESP32-S3 has demonstrable value as an experimental target for narrow language generation and constrained tool-calling. The remaining challenge is not merely to fit more parameters on a low-cost board, but to establish **useful Vietnamese-language competence under explicit, reproducible resource and safety constraints**. VN-MCU-LM supplies a proposal for pursuing that question without overstating current evidence.

## Acknowledgments

Dự án CVNSS4.0 được nhà nghiên cứu **Trần Tư Bình** tài trợ, theo thông tin do nhóm dự án cung cấp. Xin cảm ơn những nhà nghiên cứu và cộng đồng mã nguồn mở có công trình được trích dẫn và tiếp tục phát triển công cụ xử lý tiếng Việt.

## References

- **[R1]** Espressif Systems. *ESP32-S3 datasheet.* https://www.espressif.com/sites/default/files/documentation/esp32-s3_datasheet_en.pdf
- **[R2]** DaveBben. *Running a LLM on the ESP32.* https://github.com/DaveBben/esp32-llm
- **[R3]** slvDev. *Running a 28.9M parameter LLM on a microcontroller.* https://github.com/slvDev/esp32-ai
- **[R4]** Bellman281. *esp32-tinyLLM: Rust inference engine for a 28.9M PLE TinyLM.* https://github.com/Bellman281/esp32-tinyLLM
- **[R5]** andrisgauracs. *Needle 2 running fully offline on ESP32-S3.* https://github.com/andrisgauracs/needle-2-esp32
- **[R6]** Eldan, R. & Li, Y. (2023). *TinyStories.* https://arxiv.org/abs/2305.07759
- **[R7]** Nguyen, D. Q. & Nguyen, A. T. (2020). *PhoBERT.* https://aclanthology.org/2020.findings-emnlp.92/
- **[R8]** Varela Sánchez, D. & Martín-Coello Juárez, G. (2026). *Oído: Open-Vocabulary Speech Recognition on a $5 Microcontroller without a Neural Accelerator.* https://arxiv.org/abs/2610.13447
- **[R9]** Scherer, M. et al. (2024). *Deeploy: Enabling Energy-Efficient Deployment of Small Language Models on Heterogeneous Microcontrollers.* https://doi.org/10.1109/TCAD.2024.3443718
- **[R10]** MLCommons. *MLPerf Tiny Benchmark.* https://github.com/mlcommons/tiny

## Citation

~~~bibtex
@misc{ngohoang2026tinypaper,
  title        = {TinyLLM tieng Viet tren vi dieu khien ESP32-S3:
                  Thuc trang, gioi han va tiem nang},
  author       = {Ngo-Hoang, Dai-Long},
  year         = {2026},
  month        = {November},
  note         = {Research preprint; not peer reviewed},
  howpublished = {GitHub repository},
  url          = {https://github.com/xulytiengviet/Tinypaper}
}
~~~

**License notice.** Website content and original website diagrams are released under CC BY-SA 4.0 as described in [LICENSE.md](LICENSE.md). Research datasets, external model weights, and cited third-party software retain their original licenses.