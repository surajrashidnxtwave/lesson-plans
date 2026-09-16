# Lesson Plans

Static site of instructor lesson plans and student worksheets for NxtWave
Programming Foundations sessions.

Every page is a single self-contained HTML file — slide images are inlined as
base64, styles and scripts are embedded. There is no build step and no
dependencies.

## Structure

```
index.html                              landing page, links every session
plans/programming-foundations/
  11-nested-conditional-statements-plan.html
  11-nested-conditional-statements-worksheet.html
  12-loops-plan.html
  15-string-methods-plan.html
  15-string-methods-worksheet.html
vercel.json                             static config (cleanUrls)
```

## Local preview

Open `index.html` in a browser, or serve the folder:

```bash
python -m http.server 8000
```

Then visit http://localhost:8000

## Deploy

Vercel, framework preset **Other**. No build command, output directory `.`
Pushing to the default branch deploys to production; any other branch gets a
preview URL.

## Adding a session

1. Drop the self-contained HTML into `plans/<course-slug>/`.
2. Add a card for it in `index.html`.
