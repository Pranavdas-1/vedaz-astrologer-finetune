# Vedaz Astrologer Fine-Tune

LoRA fine-tuning of Qwen2.5-7B-Instruct on Vedaz's Vedic astrologer chat dataset (technical assessment submission).

## What's in this repo

- `training/` — Notebook used for data cleaning, LoRA fine-tuning, and evaluation
- `qwen-astrologer-lora-final/` — Saved LoRA adapter weights
- `docs/vllm_hosting_writeup.md` — Written explanation of how to host the model on a VPS using vLLM (as requested in the assessment)
- `docs/astrologer_chats_final.md` — 5 sample user-astrologer conversations (as requested in the assessment)

## What I did

1. **Cleaned the data** — the provided JSON file had inconsistent formatting (newline-separated objects, stray trailing commas), so I wrote a parser to load all 55 conversations reliably.
2. **Fine-tuned the model** — started with Qwen2.5-1.5B-Instruct to test the pipeline, then moved to Qwen2.5-7B-Instruct (4-bit quantized) for better output quality.
3. **Trained with LoRA** — using `trl`'s `SFTTrainer`, 4 epochs, tracking training and validation loss.
4. **Tested the output** — ran the fine-tuned model on English, Hindi, and Hinglish prompts to check consistency.

## What I found

- The model responds noticeably better in English than in Hindi/Hinglish.
- It sometimes states specific planetary positions without asking for the user's birth details first — not something a real astrologer would do, and a limitation of training on a small dataset.
- It occasionally offers crisis-helpline language even for ordinary stress, when that should be reserved for genuine emotional crises.

## Notes

The LoRA adapter is small and included directly. If it's too large for GitHub, a download link is provided instead.
