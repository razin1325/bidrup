# BD_Political_QA

**A bilingual benchmark for evaluating vision-language models on Bangladeshi political satire**

Razin Ibn Asad Molla<sup>1</sup>, Md. Toriqul Haque<sup>1</sup>, Sujon Samir<sup>1</sup>, Tuhin Hossain<sup>1</sup>, Anika Tasnim Rodela<sup>2</sup>, Mahbub E Sobhani<sup>1,&#9827;</sup>

<sup>1</sup>Dept. of Computer Science and Engineering, University of Scholars, Dhaka, Bangladesh<br>
<sup>2</sup>Dept. of Computer Science and Engineering, United International University, Dhaka, Bangladesh

&#9827; Corresponding author: mahbub@ius.edu.bd

Political memes combine images and short text to comment on leaders, parties, and events, and they spread quickly on social media. Vision-language models (VLMs) are now used to read and moderate such content, yet their understanding of political satire in low-resource languages such as Bangla has received little attention. The main barrier is the lack of a benchmark that joins a low-resource language, local political knowledge, and a test design that shows where models fail.

This repository contains the BD_Political_QA benchmark, the inference and judging code used in our study, and the evaluation results for seven VLMs under zero-shot and chain-of-thought prompting.

Paper: *Cultural Blindspots in Multimodal AI: Evaluating VLMs on Bangladeshi Political Satire* (link to be added)

## Benchmark at a glance

- 174 Bangladeshi political memes.
- 7 questions per meme: 4 open-ended and 3 multiple-choice (MCQ).
- Every question and answer is written in both English and Bangla, for a total of 2,436 question-answer pairs.
- Native speakers wrote the questions and answers, and subject matter experts (SMEs) verified them.
- The questions cover the country context, political events, the primary target of the meme, the rhetorical technique, the stance or sentiment, political bias, and the punchline.

## Models and prompting

We evaluate one proprietary and six open-source VLMs:

| Model | Access |
| --- | --- |
| Gemini 3.1 Flash-Lite | Proprietary |
| Gemma 4 E4B | Open source |
| LLaVA 13B | Open source |
| Ministral 3 8B | Open source |
| Qwen2.5 7B | Open source |
| Qwen3-VL 8B | Open source |
| InternVL3 58B | Open source |

Each model is tested in two prompting conditions:

- **Zero-shot (ZS):** the model answers directly, with no request for reasoning.
- **Chain-of-thought (CoT):** the model is asked to reason step by step before giving its final answer.

## Scoring

- **MCQ:** exact match against the gold letter.
- **Open-ended:** an LLM judge (Qwen3-0.6B) scores semantic equivalence to the gold answer and returns a decision, a confidence score, and a short reason. The judge outputs were checked against SME judgments before being used at scale.

## Key results

- Gemini 3.1 Flash-Lite gives the best overall accuracy: 79.3% in English and 76.5% in Bangla, about 10 points above the best open-source model.
- MCQ accuracy is lower than open-ended accuracy in all 28 settings (7 models x 2 languages x 2 prompting modes).
- No model exceeds 42.3% accuracy on rhetorical technique.
- CoT lowers Bangla MCQ accuracy for five of the seven models.
- The 4B Gemma 4 outperforms the 13B LLaVA by 23.9 points in Bangla.

## Repository layout

```text
.
├── Corpus/
│   ├── BD_Political_QA.json      # questions and answers in English and Bangla
│   ├── rename_images.py          # helper for normalizing image file names
│   └── images/                   # meme images, IMAGE_XXX naming
├── Notebooks/
│   ├── 01_bd_political_meme_vlm_inference.ipynb   # VLM inference over the corpus
│   └── 02_bd_political_meme_llm_judge.ipynb       # LLM judging for open-ended answers
└── Evaluation Results/
    ├── Zero shot/                # zero-shot results, one folder per model
    └── CoT/                      # chain-of-thought results, one folder per model
```

## Data format

`Corpus/BD_Political_QA.json` is a list of meme entries. Each entry has an `image_id` and a list of seven questions. Open-ended questions carry a bilingual question and a bilingual reference answer. MCQ items also carry bilingual options.

```json
{
  "image_id": "IMAGE_001",
  "questions": [
    {
      "id": "Q1",
      "type": "open_ended",
      "question": {
        "en": "Which country's political context does this image most likely relate to?",
        "bn": "..."
      },
      "correct_answer": {
        "en": "Bangladesh",
        "bn": "..."
      }
    },
    {
      "id": "Q3",
      "type": "mcq",
      "question": {
        "en": "Who or what is the primary target of this image?",
        "bn": "..."
      },
      "options": {
        "en": { "A": "Individual politicians", "B": "Political parties", "C": "Policies", "D": "Institutions", "E": "Social groups" },
        "bn": { "A": "...", "B": "...", "C": "...", "D": "...", "E": "..." }
      },
      "correct_answer": {
        "en": "A",
        "bn": "A"
      }
    }
  ]
}
```

## Running the pipeline

### 1. Dependencies

The notebooks were run with Python 3.12. Install the Python packages first:

```bash
python -m venv .venv
# Linux or macOS
source .venv/bin/activate
# Windows
.venv\Scripts\activate

pip install requests pillow tqdm transformers torch
```

The inference notebook talks to a local Ollama server. Install Ollama from https://ollama.com, pull the model you want to evaluate, and make sure the server is running on `http://localhost:11434`.

The judge notebook loads `Qwen/Qwen3-0.6B` with Hugging Face Transformers. A GPU is recommended but not strictly required for the judge.

### 2. Run VLM inference

Open `Notebooks/01_bd_political_meme_vlm_inference.ipynb` and set:

- `model_name` to the Ollama model you want to test.
- `image_dir` to `Corpus/images`.
- `OUTPUT_FILE_PATH` to where the raw VLM results should be written.
- `language` to `"en"` or `"bn"`.

The notebook asks each question separately, saves raw responses, and writes checkpoint files along the way so a long run can resume.

### 3. Run the LLM judge

Open `Notebooks/02_bd_political_meme_llm_judge.ipynb` and set the input path to a raw VLM result file and the output path for the judged results. The notebook produces per-image evaluations with `decision`, `confidence`, `short_reason`, and `thinking` fields for each question.

### 4. Reproduce the reported numbers

Run both prompting modes for each model, then compare the judged result files with the ones in `Evaluation Results/`. Each file reports overall accuracy, MCQ and open-ended breakdowns, per-question accuracy, and confidence statistics in its `evaluation_metadata` block.

## Evaluation results

Results are organized by prompting mode and model:

```text
Evaluation Results/
├── Zero shot/
│   └── Gemma4_e4b/
│       └── English/
│           └── Gemma4_e4b_English_ZeroShot_LLMJudge.json
└── CoT/
    └── Gemma4_e4b/
        └── English/
            └── Gemma4_e4b_English_CoT_LLMJudge.json
```

File names follow the pattern `{Model}_{Language}_{Mode}_LLMJudge.json`. Every result file has two top-level blocks:

- `evaluation_metadata`: totals, accuracy, MCQ and open-ended statistics, per-question accuracy, confidence statistics, and a timestamp.
- `image_evaluations`: per-image answers, gold answers, decisions, confidence values, and judge notes.

## Caveats

- Political satire is subjective. The gold answers reflect the consensus of native speakers and SMEs, not a single universal reading.
- Meme meaning depends on time and context. Events and figures that are central to this corpus may become less recognizable over time.
- The LLM judge was validated against SME labels, but it is still a model. We report judge decisions as they were produced and recommend spot checks when reusing the data.
- Results are tied to the corpus snapshot in this repository. Changes to prompts, decoding settings, or model versions can shift the numbers.

## Citation

If you use this benchmark or the evaluation results, please cite:

```bibtex
@misc{asad2026cultural,
  title  = {Cultural Blindspots in Multimodal AI: Evaluating VLMs on Bangladeshi Political Satire},
  author = {Razin Ibn Asad Molla and Md. Toriqul Haque and Sujon Samir and Tuhin Hossain and Anika Tasnim Rodela and Mahbub E Sobhani},
  year   = {2026},
  note   = {Preprint}
}
```

## Authors and contact

- Razin Ibn Asad Molla (University of Scholars): razin1325@gmail.com
- Md. Toriqul Haque (University of Scholars): toriqulhaque27@gmail.com
- Sujon Samir (University of Scholars): sujonsamir2004@gmail.com
- Tuhin Hossain (University of Scholars): tuhin@ius.edu.bd
- Anika Tasnim Rodela (United International University): anika@cse.uiu.ac.bd
- Mahbub E Sobhani (University of Scholars, corresponding author): mahbub@ius.edu.bd

## License

The code in this repository is released under the MIT License. The corpus and evaluation results are released under CC BY 4.0. Please cite the paper if you use the data.
