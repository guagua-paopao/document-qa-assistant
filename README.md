# Company Document Q&A Assistant

By Zichun Ai, MSc Internet of Things with AI, University of Bristol

A chatbot for a company's internal documents. Ask it something like "How do I claim travel expenses?" or "Why can't I connect to the VPN?", and it will:

- answer **only from documents you are allowed to see**,
- show **which document the answer came from**, and
- say **"I don't know"** when the documents don't cover the question.

This is a personal project. It connects with my Java full-stack internship (Apr–Aug 2025), where I learned how the front end and back end of a business system work together.

## How it works

1. **Upload documents** in PDF, Word, Markdown or plain text. They are checked and split into short passages.
2. **Ask a question.** The system looks for matching passages by keyword and by meaning, and only searches what you have permission to read.
3. **Get an answer.** The AI model writes it from those passages, word by word, and lists its sources.
4. **Or get a polite "I don't know"** if the evidence is too weak.

## What else it has

- An admin page for who can see what, and for usage limits
- An activity log that shows if anyone has tampered with it
- Support for different AI model providers, without changing the rest of the system
- Automatic quality checks before a new version goes out, then a gradual roll-out that can stop and roll back by itself

I built it step by step in eight stages, from planning through to a practice release.

## How it was tested

- 76 automated tests plus 35 sub-tests, covering about 89% of the code
- A 20-question check of answers, sources, refusals and permission leaks, which it passes with no leaks

## Good to know

All of this testing used made-up documents and a stand-in for the AI model. It shows the system works as designed, but not yet how good a real AI model's answers would be. One setting was also adjusted using those same 20 questions, so the score isn't a real-world estimate. So far it has only run on my own computer.

## Built with

Python · FastAPI · Next.js · TypeScript · PostgreSQL + pgvector · Redis · MinIO · Docker

Build steps and what I learned along the way are in [BUILD_NOTES.md](BUILD_NOTES.md).

## My other projects

- [people-flow-analysis](https://github.com/guagua-paopao/people-flow-analysis): people-flow analysis from live camera video
- [vehicle-type-colour-recognition](https://github.com/guagua-paopao/vehicle-type-colour-recognition): recognises vehicle type and colour from camera video
