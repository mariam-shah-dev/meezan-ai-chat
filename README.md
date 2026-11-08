# Meezan Bank redesign: AI chat

An unofficial redesign concept of Meezan Bank's website, with a help chatbot that uses a real AI model.
Not affiliated with Meezan Bank Limited.

## What's in this folder

```
meezan-ai-chat/
├── meezan-bank-redesign.html   ← the website (open it in Chrome)
└── backend/                    ← small Python server that talks to the AI
    ├── main.py                 ← the server
    ├── knowledge.py            ← the AI's rules and the facts it may use
    ├── .env.example            ← settings template (your key goes in .env)
    ├── requirements.txt
    └── tests/test_main.py      ← 29 tests, no key needed
```

## How it works

```
Website chat  →  backend (your laptop)  →  AI service (Groq)
                  - adds the secret API key
                  - adds the rules from knowledge.py
                  - blocks CNIC / card / OTP numbers
                  - limits messages per minute
```

The API key stays in `backend/.env`. It is never in the HTML, so nobody can steal it from the page.
If the backend isn't running, the chat still works with its saved (fixed) answers.

## Setup on Windows (about 10 minutes)

**1. Install Python 3.11 or newer** from python.org. On the first install screen, tick **"Add python.exe to PATH"**.

**2. Get a free Groq key** (no credit card):
go to https://console.groq.com/keys → sign up → **Create API Key** → copy it.

**3. Set up the backend.** Open the `backend` folder in VS Code, then *Terminal → New Terminal*, and run:

```
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
copy .env.example .env
```

Open the new `.env` file and replace `paste-your-groq-key-here` with your key. Save.

**4. Start the server:**

```
uvicorn main:app --reload
```

Open http://localhost:8000/api/health in Chrome. You should see `"ai_ready": true`.

**5. Open the website.** Double-click `meezan-bank-redesign.html`, open the chat (bottom corner).
The header should say **● AI answers** with a green dot. Ask anything, in English, Urdu or Roman Urdu.

Every time you come back later, you only need step 4 (and `.venv\Scripts\activate` first).

## Run the tests

From the `backend` folder (with `.venv` active):

```
python -m pytest -q
```

## Common problems

| What you see | Fix |
|---|---|
| `'uvicorn' is not recognized` | Run `.venv\Scripts\activate` first. |
| `"ai_ready": false` | The key is missing in `.env`. Add it, save, stop the server (Ctrl+C) and start it again. |
| Chat says **Saved answers** | The backend isn't running, or it was started after the page. Close and reopen the chat. |
| "AI is busy" | You hit Groq's free limit (30 messages a minute). Wait a minute. |
| Answers are in the wrong language | The AI follows the language you type in. Type in Urdu to get Urdu. |

## Change things

- **Use a different AI:** edit `.env`. It has ready lines for Google Gemini and for Ollama (a model on your own laptop, no internet).
- **Change what the AI knows:** edit `FACTS` in `backend/knowledge.py`, then restart the server.
- **Turn AI off:** in the HTML file, search for `const AI_API` and set it to `''`.
- **More messages per minute:** change `RATE_LIMIT_PER_MIN` in `.env`.

## Privacy

Chat messages are sent to the AI service (Groq) to get an answer. The site shows a note telling people not to type
personal details, and the CNIC, card, IBAN and OTP checks stop those numbers in the browser and again on the server.
The server logs only that a reply happened, never what people typed.
