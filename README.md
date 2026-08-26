# GLIM Project Viva Examiner

A Streamlit app that conducts a timed, oral, AI-led viva on a student's uploaded
project report, using Groq's `openai/gpt-oss-20b` for questioning and
`whisper-large-v3` for speech-to-text.

## Files

| File | Purpose |
|---|---|
| `viva_gen.py` | The app itself (the "engine"). Same for every deployment. |
| `viva_config.py` | Per-deployment settings: subject, difficulty, question count, time limit. **This is the only file to edit for a new subject/faculty.** |
| `requirements.txt` | Python dependencies. |
| `.streamlit/secrets.toml.example` | Template for the API keys the app needs. Copy to `secrets.toml` locally (gitignored) or paste into Streamlit Cloud's Secrets settings. |

## Deploying on Streamlit Community Cloud (recommended)

1. Push this repo to GitHub.
2. Go to [share.streamlit.io](https://share.streamlit.io), sign in, and click "New app."
3. Point it at this repo and set the main file to `viva_gen.py`.
4. In the app's **Settings → Secrets**, paste in:
   ```toml
   GROQ_API_KEY = "your-groq-api-key-here"
   GSHEETS_WEBHOOK_URL = "https://script.google.com/macros/s/XXXXXXXX/exec"  # optional
   ```
5. Deploy. You'll get a shareable URL for that subject's viva.

Get a free Groq API key at [console.groq.com](https://console.groq.com).

## Running locally

```bash
pip install -r requirements.txt
cp .streamlit/secrets.toml.example .streamlit/secrets.toml   # then fill in your key
streamlit run viva_gen.py
```

## Reusing this for another faculty member / subject

1. Duplicate this whole repo (e.g. GitHub's "Use this template" or just fork/copy it) into a new repo per faculty — e.g. `viva-finance`, `viva-marketing`.
2. In the copy, edit only `viva_config.py` — subject, difficulty, question count, time limit.
3. Deploy the copy as its own Streamlit app with its own `GROQ_API_KEY` (each key has its own free-tier daily quota, so separate keys avoid faculty competing for the same limit).
4. `viva_gen.py` never needs to change. Bug fixes/improvements can be pulled into every faculty's copy without touching their config.

## Notes

- Scores auto-save to a local `GLIM_Viva_Scores.xlsx` next to the script, and optionally post to a Google Sheet if `GSHEETS_WEBHOOK_URL` is set.
- The sidebar timer/anti-cheat controller is generated at runtime into a `viva_controller_component/` folder — it's gitignored and doesn't need to be committed.
- Never commit a real `secrets.toml` — only the `.example` template belongs in the repo.
