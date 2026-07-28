# English Day 1 Learning Hub

Static, build-free website for GitHub Pages.

## Included

- Bright, child-friendly responsive interface
- 33-slide Day 1 viewer
- Day 1 summary
- 22 vocabulary entries with English meanings, Vietnamese explanations, American IPA, stressed-syllable SOUND IT OUT guides, and Merriam-Webster audio
- Merriam-Webster Intermediate Dictionary (`sd3`) key configured once in `config.js`
- A separate 10-question concept quiz and 8 first-letter picture vocabulary puzzles
- Optional Merriam-Webster audio hints; results appear only after full submission

## Deploy

1. Copy everything in this folder to the root of your GitHub Pages repository.
2. Commit and push.
3. In GitHub, open **Settings → Pages**.
4. Select **Deploy from a branch**, then choose the branch and `/ (root)`.

`index.html` is the entry point. Add the API key to `MW_API_KEY` in `config.js`
before publishing if Merriam-Webster audio is required. Slide images are stored
in `assets/slides`.
