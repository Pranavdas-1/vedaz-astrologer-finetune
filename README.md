# Vedaz Astrologer Fine-Tune

Fine-tuning Qwen2.5-7B-Instruct on Vedaz's Vedic astrologer chat dataset using LoRA, with a vLLM-based VPS hosting guide and manually written sample training data.

## What's in this repo

- `training/` — Notebook/script for data cleaning, LoRA fine-tuning, and evaluation
- `qwen-astrologer-lora-final/` — Saved LoRA adapter weights (or a link if too large for GitHub — see note below)
- `docs/vllm_hosting_writeup.md` — Step-by-step guide to hosting the fine-tuned model on a VPS using vLLM
- `docs/astrologer_chats_final.md` — 5 manually written sample conversations for future training data

## Approach

1. **Data cleaning** — the provided JSON was newline-delimited with inconsistent trailing commas; wrote a parser to robustly load all 55 conversations (`training/clean_data.py`).
2. **Base model** — started with Qwen2.5-1.5B-Instruct to validate the pipeline, then moved to Qwen2.5-7B-Instruct (loaded in 4-bit via bitsandbytes) once multilingual output quality became the priority.
3. **Fine-tuning** — LoRA (`r=32, alpha=64`) via `trl`'s `SFTTrainer`, 4 epochs, tracked both training and validation loss.
4. **Evaluation** — tested outputs across English, Hindi, and Hinglish prompts.

## Key findings / limitations

- The model performs noticeably better in English than Hindi/Hinglish — likely a combination of the base model's stronger English capability and the limited per-language training examples (55 conversations split across three language styles).
- Without access to a real ephemeris or the user's actual birth chart, the model can hallucinate specific planetary transits (e.g., stating "Mars is in Capricorn" without birth details) — a known limitation of LLM-only astrology chatbots. Fixing this properly would require either more training examples that consistently gate any astrological claim behind asking for birth details, or integrating an actual astrological calculation tool rather than relying on the model's imitation of astrological language.
- The model sometimes over-applies crisis/helpline language to ordinary stress rather than reserving it for genuine emotional crises — addressed partially via a stricter system prompt, though a full fix would need more contrastive training examples (ordinary stress vs. genuine crisis).

## Hosting

See [`docs/vllm_hosting_writeup.md`](docs/vllm_hosting_writeup.md) for the full VPS + vLLM deployment process.

## Sample training data

See [`docs/astrologer_chats_final.md`](docs/astrologer_chats_final.md) for 5 manually written conversations matching the dataset's style (kundli-based reasoning, "please wait a minute for analysis," empathetic tone, and non-fatalistic future timing).
