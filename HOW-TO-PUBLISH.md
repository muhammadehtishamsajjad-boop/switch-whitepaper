# Publishing this whitepaper on GitBook

These files are ready to import. GitBook reads `SUMMARY.md` to build the
left-hand navigation, exactly like the VozDex whitepaper.

## Route A — GitHub sync (what VozDex uses, recommended)

1. Create a **public** GitHub repo, e.g. `switch-whitepaper`.
2. Upload every file in this folder to the repo root (keep the filenames).
3. Sign up at **gitbook.com** (free plan is fine) and create a new Space.
4. In the Space: **Configure → Git Sync → GitHub**, authorise, pick the repo
   and the `main` branch. GitBook imports and builds the nav from `SUMMARY.md`.
5. **Publish → Public** to get a live URL.
6. **Custom domain:** point a CNAME record for `whitepaper.yourdomain.com`
   at the host GitBook gives you, then enter that domain in GitBook's
   domain settings. This is how `whitepaper.vozdexai.com` works.

Editing later: change the Markdown in GitHub and GitBook updates itself.

## Route B — straight import, no GitHub

New Space → **Import** → upload these `.md` files. Faster to start, but you
lose the sync, so future edits happen inside GitBook rather than in the files.

## Then update the site

In `index.html`, change one line near the bottom of the script:

```js
paper : "whitepaper.html",   →   paper : "https://whitepaper.yourdomain.com",
```

Every Whitepaper button on the site reads from that one value.

## Notes

- `{% hint %}` blocks are GitBook's callout syntax — they render as coloured
  panels, the same as the notes in the HTML version.
- Formulas sit in code blocks so they keep their spacing. If you want proper
  typeset maths later, GitBook supports LaTeX blocks.
- Section order and numbering come from `SUMMARY.md` — reorder there.
