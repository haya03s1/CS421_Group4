# Evaluating Pragmatic Understanding of Saudi Arabic in Large Language Models: A Comparative Study

**Phase I Report · Track 2: Comparative Study**
CS421 · Group 4 · Section 63 · King Faisal University, Al Ahsa, Saudi Arabia

| | |
|---|---|
| **Authors** | Ghala Alotaibi (223047156), Salha Almarri (223042287), Haya Almuslim (223034961) |
| **Department** | Computer Science, King Faisal University |
| **Instructor** | Dr. Hala Hamdoun |
| **Submission date** | 1 October 2026 |
| **Repository** | https://github.com/haya03s1/CS421_Group4 |

---

## Abstract

Large language models (LLMs) are increasingly used in Arabic applications, but their ability to understand *pragmatic* meaning in Saudi Arabic has barely been examined. Existing Arabic benchmarks focus on Modern Standard Arabic (MSA) and general knowledge, and dialectal resources such as Absher assess vocabulary and cultural knowledge rather than inference about intended meaning. No current benchmark isolates pragmatic phenomena such as sarcasm, indirect speech acts, and implicature in Saudi dialects, and results across earlier studies are hard to compare because they use different prompts and evaluation settings.

This project fills that gap by building a Saudi-dialect pragmatic inference evaluation set and running a controlled comparison of four LLMs: the Saudi-developed **ALLaM** (baseline), the Arabic-native **Jais**, and the general-purpose multilingual **Qwen2.5-7B-Instruct** and **LLaMA-3-8B-Instruct**. All models are evaluated with the same zero-shot prompt, temperature-zero decoding, and answer-extraction method, so that observed differences can be attributed to pragmatic competence rather than general Arabic proficiency. We report overall and per-phenomenon accuracy together with a qualitative error analysis.

**Keywords:** Saudi Arabic, pragmatics, large language models, comparative evaluation, dialectal NLP

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Literature Review](#2-literature-review)
3. [Experimental Design](#3-experimental-design)
4. [Conclusion and Next Steps](#4-conclusion-and-next-steps)
5. [References](#references)

---

## 1. Introduction

### Background

Understanding intended meaning depends not only on the literal meaning of words but on the context in which they are said. With sarcasm, indirect expressions, idioms, and proverbs, the intended meaning can differ from the literal one. This is harder in the Saudi dialect because of dialect diversity across the regions of the Kingdom [1]. Standard Arabic and dialect are also mixed in daily speech, and these expressions rely on culture and context shared between speakers. General proficiency in Arabic therefore does not guarantee that a model understands such meanings in Saudi dialect, which makes it a separate task.

### Motivation

LLMs are increasingly deployed for users in Saudi Arabia and the Gulf, in smart assistants, customer service, and content moderation. A model that misreads sarcasm, an indirect request, or an idiom may interpret user text wrongly and respond inappropriately. Such errors can go unnoticed when evaluations do not test intended meaning directly. It is therefore important to know how well current models understand Saudi-dialect meaning and where they struggle.

### Problem Statement

A growing body of work evaluates Arabic LLMs on dialects, cultural knowledge, and some forms of non-literal language [1]–[8]. However, no study directly and fairly compares multiple models on understanding intended meaning in the Saudi dialect, using one method and identical conditions across all models. A comparison that targets this skill independently of general Arabic ability is needed.

### Objectives

1. Curate a Saudi Arabic pragmatics evaluation set covering three phenomena (sarcasm, indirect speech acts, idiomatic/proverbial meaning), with 6 items per phenomenon in Phase I (18 items in total).
2. Evaluate four LLMs, including one clearly designated baseline, under an identical zero-shot prompting protocol with temperature = 0 and the same answer-extraction method.
3. Measure and compare accuracy overall and per phenomenon, with scores directly comparable across all four models.
4. Identify systematic failure patterns through error analysis: classify each error by category and count each error type per model, on both the pragmatics task and the emotion-classification task.

---

## 2. Literature Review

### Thematic Structure

| Theme | Focus | Works |
|---|---|---|
| **A** | General Arabic LLM benchmarking (macro-evaluation) | ORCA [5] |
| **B** | Dialect- and culture-specific benchmarks | Absher [1], AraDiCE [2], PALM [6], DialectalArabicMMLU [7] |
| **C** | Figurative / pragmatic-adjacent evaluation | JAWAHER [3] (proverbs), ArSarcasm [4] (sarcasm and irony) |
| **D** | Pragmatics-specific diagnostic evaluation | ALPS [9] (mainly MSA) |
| **E** | Public dialectal resource used experimentally here | ArPanEmo [8] |

### Synthesis

**Theme A: ORCA.** Elmadany et al. [5] introduced ORCA, which gathers 60 existing datasets into seven task clusters and scores 18 Arabic and multilingual models on a single leaderboard. Its strength is breadth, and its design (fixed inputs, one metric per cluster, a public leaderboard) is a useful template for the smaller, phenomenon-specific benchmark proposed here. It does not measure pragmatics, though: sarcasm and indirection appear, if at all, as noise inside sentiment or inference scores.

**Theme B: Dialects and culture.**
- **AraDiCE** [2] machine-translated seven datasets into MSA and several dialects, post-edited them by hand, and added a cultural-awareness test for the Gulf, Egypt, and the Levant. Arabic-native models such as Jais and AceGPT beat general multilingual models on dialectal tasks, but all models struggled with dialect identification, generation, and translation.
- **PALM** [6] was written by hand by 44 researchers across all 22 Arab countries, in both MSA and dialects. Frontier LLMs showed sharp gaps between countries, with Egypt and the UAE better served than Iraq, Mauritania, or Yemen.
- **DialectalArabicMMLU** [7] translated 3,000 academic questions into five dialects, including Saudi, yielding about 15,000 question–answer pairs, and tested 19 open-weight models. Gaps between dialects persisted even on simple factual questions.
- **Absher** [1] is the first benchmark dedicated to Saudi dialects and culture, with over 18,000 multiple-choice items on words, phrases, and proverbs from five Saudi regions.

Saudi-aware resources therefore exist, but they focus on recognizing words, translating accurately, or recalling facts. None asks a model to work out what a speaker implies in a short exchange.

**Theme C: Figurative and pragmatic-adjacent work.** Magdy et al. [3] compiled JAWAHER, a multidialectal proverb set with idiomatic translations and human explanations. LLMs often produce a fluent, idiomatic translation of a proverb yet fail to explain its meaning in context, which motivates our error analysis of the gap between fluent output and real comprehension. Abu Farha and Magdy [4] released ArSarcasm by re-annotating existing Arabic datasets for dialect and sarcasm. Sarcasm made up only about 16% of its 10,547 tweets, and a BiLSTM baseline reached an F1 of just 0.46 on the sarcastic class, showing how hard the task is and how scarce good sarcasm resources are. It predates instruction-tuned LLM prompting and draws on pan-Arabic rather than Saudi-specific data.

**Theme D: ALPS.** Al-Olimat and Alshareef [9] built ALPS, an expert-written diagnostic set of 531 questions across roughly 15 tasks and 47 subtasks, testing deep semantics and pragmatics, including implicature and speech acts, rather than knowledge or translation quality. After testing 23 open-source and commercial models, they reported a clear gap between surface fluency and deeper linguistic proficiency. ALPS is the strongest evidence so far that pragmatic reasoning can be isolated and measured in Arabic, but its scope is pan-Arabic and leans toward MSA. Its items are not organized by dialect, so Saudi-specific phenomena (the indirection and proverbial style of Najdi, Hijazi, and Gulf-adjacent speech) are not separated out.

**Theme E: ArPanEmo.** Althobaiti [8] provides a public dataset for fine-grained Arabic emotion recognition, focused on the Saudi dialect. It contains over 11,000 manually labelled posts from multiple online platforms, with emotion categories plus a neutral category. It was not designed to test sarcasm, indirect speech acts, idioms, or proverbs, so we use it as a *secondary* evaluation of context and emotion understanding alongside the main pragmatics task.

### Closing Argument

Taken together, Themes A–D make three points:

1. General-purpose benchmarks treat pragmatic competence as a byproduct of sentiment or inference scores rather than a skill measured in its own right.
2. Dialect and culture benchmarks (AraDiCE, PALM, DialectalArabicMMLU, Absher) show that Saudi and Gulf dialects can be evaluated at scale, but their tasks are built around lexical recognition, translation reliability, or factual recall, not inference of non-literal meaning from context.
3. The work closest to pragmatics solves only half of the problem: JAWAHER and ArSarcasm are dialect-aware but not Saudi-specific (and neither tests inference of intended meaning with modern LLM prompting), while ALPS isolates pragmatic reasoning but pools all Arabic varieties.

Each study also uses its own prompt format and metrics. A controlled comparison of several LLMs on Saudi-dialect pragmatic inference under one protocol is therefore still missing.

---

## 3. Experimental Design

### Task Definition

Each model receives a short text or conversation in Saudi dialect and four answer choices, and must select the correct intended meaning. The task covers:

- Sarcasm
- Indirect speech acts / indirect expressions
- Idioms and proverbs

ArPanEmo [8] serves as a secondary evaluation of context and emotion understanding.

### Candidate Models and Baseline

| Role | Model | Category | Rationale | Reported accuracy (from Absher [1]) | Tested domain |
|---|---|---|---|---|---|
| **Baseline** | ALLaM 3rd 7B | Arabic-native (Saudi) | Saudi-aligned; natural baseline | Outperformed each of the other five models on proverb comprehension, with 48.74% | Proverbs (cultural interpretation) |
| Candidate | Jais-13B | Arabic-native | Open-weight Arabic model | 41.81% on proverbs, 41.94% on phrases, 42.66% on words | Proverbs, phrases, words |
| Candidate | LLaMA-3-8B-Instruct | General multilingual | Contrast: Arabic-specific vs. general failure | Second on proverbs (45.31%); 43.41% on phrases | Proverbs, phrases |
| Candidate | Qwen2.5-7B-Instruct | General multilingual | Mid-tier multilingual contrast | Best of the remaining models on word-level vocabulary, 63% | Words, vocabulary |

### Dataset

**Primary task.** Text and short conversations written directly in Saudi dialect (to avoid translation artefacts) covering sarcasm, indirect expressions, and idioms/proverbs. The current dataset has **18 items (6 per phenomenon)** and will be expanded to **15–20 items per phenomenon in Phase II**.

**Secondary task.** ArPanEmo [8], used to evaluate context understanding and emotion recognition.

### Evaluation Protocol and Metrics

All models are evaluated under identical conditions in a zero-shot pipeline:

- the same prompt template
- temperature = 0 decoding
- one fixed answer-extraction approach applied to every model

**Metric:** accuracy (correct answers ÷ total questions), computed separately for sarcasm, indirect expressions, and idioms/proverbs, as well as overall.

**Reporting.** Pragmatics and emotion-classification results are reported separately rather than merged into one score, because they have different chance levels:

| Task | Answer options | Chance accuracy |
|---|---|---|
| Pragmatic inference | 4 | 25% |
| ArPanEmo emotion classification | 11 | ≈ 9% |

**Error analysis.** Model errors are categorized and counted per model to find which cases are hardest. For ArPanEmo, each error is classified as a **same-valence confusion** or a **cross-valence confusion**.

---

## 4. Conclusion and Next Steps

Prior research has evaluated LLMs on dialects, cultural knowledge, and some forms of non-literal Arabic [1]–[8], but there is still a gap in evaluating whether models understand *intended meaning* in the Saudi dialect, compared across several models under the same conditions. This study aims to close that gap by comparing multiple LLMs on sarcasm, indirect expressions, and idioms/proverbs, with ArPanEmo as an additional context and emotion evaluation.

**Phase II plan:**
- [ ] Finalize and expand the dataset (15–20 items per phenomenon)
- [ ] Run the four selected models under the shared zero-shot protocol
- [ ] Collect results and compare them using the defined metrics
- [ ] Analyze errors to identify the hardest cases

---

## References

[1] R. Al-Monef, H. Alhuzali, N. Alturayeif, and A. Alasmari, "From words to proverbs: Evaluating LLMs' linguistic and cultural competence in Saudi dialects with Absher," *Alexandria Engineering Journal*, vol. 137, pp. 25–41, Feb. 2026. doi: [10.1016/j.aej.2025.12.066](https://doi.org/10.1016/j.aej.2025.12.066)

[2] B. Mousi, N. Durrani, F. Ahmad, M. A. Hasan, M. Hasanain, T. Kabbani, F. Dalvi, S. A. Chowdhury, and F. Alam, "AraDiCE: Benchmarks for dialectal and cultural capabilities in LLMs," in *Proc. 31st Int. Conf. Computational Linguistics (COLING)*, Abu Dhabi, UAE, 2025, pp. 4186–4218.

[3] S. M. Magdy, S. Y. Kwon, F. Alwajih, S. T. Abdelfadil, S. Shehata, and M. Abdul-Mageed, "JAWAHER: A multidialectal dataset of Arabic proverbs for LLM benchmarking," in *Proc. 2025 Conf. North American Chapter of the ACL: Human Language Technologies (NAACL-HLT)*, Albuquerque, NM, 2025, pp. 12320–12341.

[4] I. Abu Farha and W. Magdy, "From Arabic sentiment analysis to sarcasm detection: The ArSarcasm dataset," in *Proc. 4th Workshop on Open-Source Arabic Corpora and Processing Tools*, Marseille, France, 2020, pp. 32–39.

[5] A. Elmadany, E. M. B. Nagoudi, and M. Abdul-Mageed, "ORCA: A challenging benchmark for Arabic language understanding," in *Findings of the Association for Computational Linguistics: ACL 2023*, Toronto, Canada, 2023, pp. 9559–9586.

[6] F. Alwajih *et al.*, "PALM: A culturally inclusive and linguistically diverse dataset for Arabic LLMs," in *Proc. 63rd Annual Meeting of the ACL*, Vienna, Austria, 2025, pp. 32871–32894.

[7] M. Altakrori *et al.*, "DialectalArabicMMLU: Benchmarking dialectal capabilities in Arabic and multilingual language models," ELRA, 2026. Accessed: Sept. 26, 2026. [Online]. Available: https://aclanthology.org/2026.lrec-1.251.pdf

[8] M. J. Althobaiti, "An open-source dataset for Arabic fine-grained emotion recognition of online content amid COVID-19 pandemic," *Data in Brief*, vol. 51, Art. no. 109745, Dec. 2023. doi: [10.1016/j.dib.2023.109745](https://doi.org/10.1016/j.dib.2023.109745)

[9] H. S. Al-Olimat and A. Alshareef, "ALPS: A diagnostic challenge set for Arabic linguistic & pragmatic reasoning," Feb. 19, 2026. Accessed: Sept. 26, 2026. [Online]. Available: https://arxiv.org/abs/2602.17054v1
