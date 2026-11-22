# Cultural Blindspots in Multimodal AI: Evaluating VLMs on Bangladeshi Political Satire

### Accepted for Publication in 2026 29th International Conference on Computer and Information Technology (ICCIT) 🎉
*18–20 December 2026, Cox’s Bazar, Bangladesh*



> 🌐 **TL;DR:** BD_Political_QA introduces a 176-meme bilingual benchmark spanning 2,464 verified QA pairs in English and Bangla that enables multimodal political satire evaluation, revealing how model scale, cultural context, and chain-of-thought prompting shape reasoning performance.

**Abstract:** *Political memes combine images and short text to comment on leaders, parties, and events, and they spread quickly on social media. Vision-language models (VLMs) are now used to read and moderate such content, yet their understanding of political satire in low-resource languages such as Bangla has received little attention. The main barrier is the lack of a benchmark that joins a low-resource language, local political knowledge, and a test design that shows where models fail. Here, we introduce BD_Political_QA, a bilingual benchmark of 176 Bangladeshi political memes, each with seven multiple-choice (MCQ) and open-ended questions in English and Bangla. Its 2,464 question-answer pairs were written by native speakers and verified by subject matter experts (SMEs). We evaluate one proprietary and six open-source VLMs under zero-shot (ZS) and chain-of-thought (CoT) prompting, using exact match for MCQ and an SME-checked LLM judge for open-ended answers. Gemini 3.1 Flash-Lite achieves the best overall accuracy of 79.3% in English and 76.5% in Bangla, about 10 points above the best open-source model. Furthermore, MCQ accuracy is lower than open-ended accuracy in all 28 settings, and no model exceeds 42.3% on rhetorical technique. In contrast to common expectations, CoT lowers Bangla MCQ accuracy for five of seven models, and the 4B Gemma 4 outperforms the 13B LLaVA by 23.9 points in Bangla.*

### Cite

```bibtex
@inproceedings{molla2026cultural,
  title={Cultural Blindspots in Multimodal AI: Evaluating VLMs on Bangladeshi Political Satire},
  author={Molla, Razin Ibn Asad and Haque, Md. Toriqul and Samir, Sujon and Hossain, Tuhin and Rodela, Anika Tasnim and Sobhani, Mahbub E},
  booktitle={2026 29th International Conference on Computer and Information Technology (ICCIT)},
  year={2026},
  month={December},
  address={Cox's Bazar, Bangladesh}
}
```
