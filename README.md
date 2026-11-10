# Company Document Q&A Assistant

By Zichun Ai, MSc Internet of Things with AI, University of Bristol

It's a chatbot for a company's internal documents, so you can ask it things like "How do I claim travel expenses?" or "Why can't I connect to the VPN?".

- It only answers from documents you're allowed to see.
- It shows you which document the answer came from.
- It says "I don't know" when the documents don't cover the question.

This is a personal project, and it ties in with my Java full-stack internship (Apr–Aug 2025), where I learned how the front end and back end of a business system work together.

## How it works

1. You upload documents in PDF, Word, Markdown or plain text, and it checks them and splits them into short passages.
2. You ask a question, and it looks for matching passages by keyword and by meaning, but only in what you're allowed to read.
3. The AI model writes the answer from those passages, word by word, and lists its sources.
4. If the evidence is too weak, you get a polite "I don't know".

## What else it has

- There's an admin page for who can see what and for usage limits.
- The activity log shows if anyone has tampered with it.
- You can switch AI model providers without changing the rest of the system.
- New versions go through automatic quality checks, then a gradual roll-out that can stop and roll back by itself.

I built it step by step in eight stages, from planning through to a practice release.

## How it was tested

- There are 76 automated tests plus 35 sub-tests, covering about 89% of the code.
- It passes a 20-question check of answers, sources, refusals and permission leaks, with no leaks.

## Good to know

All of this testing used made-up documents and a stand-in for the AI model, so it shows the system works as designed but not how good a real AI model's answers would be. I also adjusted one setting using those same 20 questions, so the score isn't a real-world estimate. So far it's only run on my own computer.

## Built with

Python · FastAPI · Next.js · TypeScript · PostgreSQL + pgvector · Redis · MinIO · Docker

Build steps and what I learned along the way are in [BUILD_NOTES.md](BUILD_NOTES.md).

## My other projects

- [people-tracking-and-counting](https://github.com/guagua-paopao/people-tracking-and-counting): tracks and counts people from live camera video
- [vehicle-type-colour-recognition](https://github.com/guagua-paopao/vehicle-type-colour-recognition): recognises vehicle type and colour from camera video
