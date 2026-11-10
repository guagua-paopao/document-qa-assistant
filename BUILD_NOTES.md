# Build Notes

How to run this project, how I put it together, and what I learned.

## Run it

You need Python 3.12, Node.js 22 and Docker 26 or newer. In PowerShell:

```powershell
Copy-Item .env.example .env
# Edit .env and replace every CHANGE_ME value. Do not commit this file.
.\scripts\setup.ps1
.\scripts\check.ps1 -OnlineAudit
.\scripts\dev.ps1
```

In a second terminal, create a 30-minute local token and try it:

```powershell
$token = & .\.venv\Scripts\python.exe apps\api\scripts\issue_dev_token.py demo
Invoke-RestMethod http://127.0.0.1:8000/api/v1/me -Headers @{Authorization="Bearer $token"}
```

To run the 20-question check while the services are up:

```powershell
.\.venv\Scripts\python.exe scripts\evaluate_s4.py
```

Local mode uses a stand-in AI model and local tokens. A production configuration refuses to start with them.

## How I built it

I built it in eight stages, and each one had to pass its tests before the next began.

1. **Define the job.** Two uses (staff policy questions and IT support), the safety limits, and a set of made-up test questions.
2. **Login and separate companies.** Single sign-on, with each user's company taken from their verified login.
3. **Connecting to AI models.** One layer that can talk to different providers, with answers streamed word by word, plus stop and retry.
4. **Taking in documents.** Secure upload, reading four file formats, splitting into passages, and publishing a new version all at once.
5. **Answers with sources.** Search by keyword and by meaning, merge and re-rank the results, cite sources, and refuse when the evidence is weak.
6. **Company controls.** Group permissions, usage limits, and an activity log that shows tampering.
7. **Quality and reliability.** Comparing each version with a baseline, monitoring, load tests, deliberately causing failures, and recovery drills.
8. **A practice release.** Sign-offs, then a gradual roll-out (5%, 25%, 50%, 100%) that can stop and roll back by itself.

## What I learned

- **Check permissions before searching, not after.** Both searches run only inside the documents the user may read. Forbidden documents cannot take up top results or reveal that they exist.
- **Do not trust the model's citations.** The model can only cite source IDs the server gave it for that question, and the answer is held back until those citations have been checked.
- **If the evidence is weak, do not call the model.** Refusing before the model runs lowers the risk of leaks and made-up answers, and costs nothing.
- **Treat documents as untrusted.** A document can contain instructions aimed at the model, so suspicious sentences are removed before the model sees the text.
- **Keyword and meaning scores cannot be compared directly.** The two result lists are merged by rank instead (weighted reciprocal rank fusion).
- **Keep the failing test.** In the first test run, "Where can employees park bicycles?" matched a policy document only because of the word "employees", and the system gave a wrong answer. I kept the question, raised the share of question words that must match from 20% to 34%, and re-ran all 20 questions to confirm answerable ones still got answers. Because that setting was adjusted on the same questions, the score is not a real-world estimate.
