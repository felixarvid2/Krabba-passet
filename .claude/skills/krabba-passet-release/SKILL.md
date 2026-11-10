---
name: krabba-passet-release
description: Ship a change to Krabba-passet (felixarvid2/Krabba-passet) - version bump checklist, test suite, PR, republishing the claude.ai artifact, and building the release zip. Use for any change to the game that is to be shipped, when the artifact must be republished, or when a new Krabba-passet release is wanted.
---

# Shipping Krabba-passet

The game is the whole of felixarvid2/Krabba-passet (static HTML/JS, three.js r158, namespace `NV`), with
`index.html` at the repo root. It lived in `natverksspel/` of felixarvid2/test until version 11.1.
The owner writes in Swedish; answer in Swedish. Standing workflow for every request: implement -> README +
"what's new" -> full test suite -> commit -> push -> PR -> merge -> republish the artifact -> short summary.

## Version bump (every shipped change)

1. `index.html`: every `?v=X.Y` (about 55 tags): `sed -i 's/?v=11\.1/?v=11.2/g' index.html`.
2. `js/ui/more.js`: `var VERSION = '...'` and a new first `<li><b>Version X.Y:</b> ...</li>` in `whatsNew`.
3. Tests: every `'X.Y'` (`lastVersion`) in `test/*.js`, or the news dialog covers the menu and tests fail:
   `grep -rl "'11\.1'" test | xargs sed -i "s/'11\.1'/'11.2'/g"` (the quotes keep IPs like 10.0.0.1 safe).
4. `README.md`: a `## Version X.Y: ...` section above the previous one (cause, fix, which test checks it).
   Big releases number every item and use the same numbers in code comments.
5. New controls go in the README "Styrning" table and the F1 help (`help-v10` in `js/world/v10.js`).

## Tests

- Node: `node test/levels.js` ("Alla veckor OK"), `node test/build.js` ("Alla byggen OK"), `node test/kurs2.js`
  ("Alla kurs 2-veckor OK", weeks 11–14 of course 2).
- Browser (Playwright, server on port 8765 in the repo root: `npx http-server -p 8765 -s -c-1 .`, see the
  `playwright-webgl-testing` skill): `v10 hd2d v9 boot ui flow features v3 v4 v5 v8 build_ui report kurs2_ui
  offline`. Each must end with "inga fel" (flow prints JSON) and have 0 `pageerror`. Run them one at a time: two
  browsers at once make the first SwiftShader frame so slow that `page.screenshot` hits its 30 s timeout in ui,
  features, v3 and v4. `test/offline.js` needs no server (it opens a copy over `file://`). `test/view.js` is a
  screenshot tool that takes arguments, not a test.
- New behaviour gets a check in the latest feature test; prove it fails without the fix.

## Git and PR

- Default branch `main`. Every PR is merged, so start each change from main:
  `git fetch origin main && git checkout -B <branch> origin/main` (stash first if needed), then push normally.
- The owner's standing request for this game is "create the PR and merge it" (merge commit).

## Republish the artifact

URL: https://claude.ai/artifact/Y1dRntTjUpHWHGZRUuKt17 (page file in the scratchpad: `krabba-passet.html`).
The page is `index.html`'s body with all CSS inlined; JS files are published next to it:
```python
import re
idx = open('index.html').read()
css = re.findall(r'href="(css/[^"?]+)', idx)
body = idx[idx.index('<body>') + 6: idx.index('</body>')]
page = '<title>Krabba-passet</title>\n<style>\n:root{color-scheme:dark}\n' + ''.join(open(c).read() for c in css) + '</style>\n<!--v7css-->\n' + body
```
Publish with `url` = the artifact, `root` = path to the repo root **relative to the current directory**
(`.` when inside it), and `files` = only the JS files changed since the last publish. CSS changes ride in the
page. A file published from another session must be read (`action: read`, `path`) before it can be
overwritten; diff it against git first. Artifacts cannot serve `.glb` (models are stored as `.gltf.json`).
Never publish `assets3d/offline.js`: the artifact is served over https and does not load it.

## Release zip

1. Copy exactly the committed game: `git archive <commit> | tar x -C <dir>/Krabba-passet-X.Y`, then delete
   `test/`, `docs/` and `.claude/` from that folder.
2. Run `node tools/offline.js <dir>/Krabba-passet-X.Y` so `assets3d/offline.js` (git-ignored, about 11 MB) is in
   the zip: it lets players double-click `index.html` with no web server and still get the 3D models.
3. Zip it with the top folder `Krabba-passet-X.Y/`, unzip into an empty folder and open `index.html` over
   `file://` in 2D, HD-2D and 3D (0 page errors, 0 blocked files, `NV.assets3d.ready()` in 3D), and once over a
   server on another port (0 HTTP 4xx).
4. Cloud sessions get HTTP 403 both for `POST /repos/.../releases` and for pushing tags. Do not retry; send the
   zip with SendUserFile and give the owner the steps: Releases -> Draft new release -> tag `vX.Y` (create on
   publish, target main) -> attach zip -> paste the release notes. Repo "Code -> Download ZIP" also works, but
   lacks `offline.js` until `node tools/offline.js` is run.
5. README screenshots live in `docs/*.jpg` (HD-2D hero, then 2D / 3D / terminal table).
