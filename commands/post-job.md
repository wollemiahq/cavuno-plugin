---
description: Post one job, or a list of up to 100, to the Cavuno board as drafts for review.
argument-hint: "[pasted job text, a job URL, or a list of roles]"
---

Use the `post-jobs` skill to turn the input below into Cavuno jobs.

Input: $ARGUMENTS

If the input is empty, ask for the job text, a job URL or a list. Create
drafts unless the user asks to publish, show what will be created before
writing, and finish with each job's ID, title, status and link.
