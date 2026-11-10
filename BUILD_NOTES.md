# Build Notes

How to run this, the order I built it in, and what I learned.

## Run it

You'll need Python 3.12, Node.js 22 and Docker 26 or newer. In PowerShell:

```powershell
Copy-Item .env.example .env
# Edit .env and replace every CHANGE_ME value. Do not commit this file.
.\scripts\setup.ps1
.\scripts\check.ps1 -OnlineAudit
.\scripts\dev.ps1
```

In a second terminal, you can make a 30-minute local token and try it out:

```powershell
$token = & .\.venv\Scripts\python.exe apps\api\scripts\issue_dev_token.py demo
Invoke-RestMethod http://127.0.0.1:8000/api/v1/me -Headers @{Authorization="Bearer $token"}
```

To run the 20-question check while everything's up:

```powershell
.\.venv\Scripts\python.exe scripts\evaluate_s4.py
```

Locally it uses a stand-in AI model and local tokens, and a production config refuses to start with them.

## How I built it

I built it in eight stages and didn't start the next one until the last one passed its tests.

1. First I worked out what it was for (staff policy questions and IT support), set the safety limits and wrote a set of made-up test questions.
2. Then I added login, with each user's company taken from their verified sign-in.
3. I added one layer that can talk to different AI providers, with streaming answers plus stop and retry.
4. Next was taking in documents: secure upload, reading four file formats, splitting them into passages and publishing each new version in one go.
5. Then came the main part, which searches by keyword and by meaning, merges and re-ranks the results, cites sources and refuses when the evidence is weak.
6. I added group permissions, usage limits and an activity log that shows if anyone's tampered with it.
7. For reliability I compared each version against a baseline, added monitoring and load tests, broke things on purpose and ran recovery drills.
8. Finally I did a practice release with sign-offs and a gradual roll-out (5%, 25%, 50%, 100%) that can stop and roll back by itself.

## What I learned

- Permissions get checked before the search runs, so documents you can't read can't take up the top results or give away that they exist.
- The model can only cite source IDs the server gave it, and the answer doesn't go out until those citations have been checked.
- If the evidence is weak the model never gets called, which means less risk of leaks or made-up answers and no cost.
- Documents can contain instructions aimed at the model, so suspicious sentences get stripped out before it sees the text.
- Keyword and meaning scores can't be compared directly, so I merge the two result lists by rank.
- In the first test run, "Where can employees park bicycles?" matched a policy document just because of the word "employees" and got a wrong answer, so I kept the question, raised the share of question words that have to match from 20% to 34%, and re-ran all 20 to check the answerable ones still got answers.
