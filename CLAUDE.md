# Krabba-passet

A network-technician game for the courses Nätverksteknik (weeks 1–10) and Routing och switching (weeks 11–14):
static HTML/JS with three.js r158 in `lib/`, namespace `NV`, views 2D, HD-2D and 3D. `index.html` is at the
repo root. The project moved here from `natverksspel/` in felixarvid2/test in version 11.1.

- The owner writes in Swedish; answer in Swedish, and keep the game's texts and the README in Swedish.
- Ship every change with the `krabba-passet-release` skill: version bump, README and "what's new", the full
  test suite, PR, merge into `main` (merge commit; standing approval from the owner), republish the artifact.
- Code map: `js/net/` (model, simulator, OSPF/BGP), `js/cli/` (Cisco IOS, hosts, WLC, LB), `js/levels.js` and
  `js/kurs2.js` (weeks), `js/world/` (3D, HD-2D, 2D), `js/ui/` (menus, dialogs, handbook), `assets3d/` (CC0
  models and textures, licenses in `assets3d/LICENSES/`), `tools/offline.js` (bundle for `file://`).
- Tests live in `test/`; see the skill for the list and for running browser tests one at a time.
