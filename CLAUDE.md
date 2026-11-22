# CLAUDE.md: Family Coach

A private parenting-coach PWA: a 60-second daily log, the 6-week screen-time taper plan with weekly checklists,
a Sunday review with pre-agreed tripwires and trends, and in-the-moment scripts. Single-file vanilla
HTML/CSS/JS (`index.html`) + `sw.js` + `manifest.json` + icons. No build step, no dependencies.

## Privacy: this repo is PUBLIC (GitHub Pages on a free plan)
- **Never put the family's names, school, notes or any logged data in this repo.** Plan content uses
  generic wording ("your daughter", "Son"/"Daughter" defaults, renamed on the phone).
- The log lives in the phone's localStorage (`fc_data`). Automatic backup goes to a **separate private
  repo** via a fine-grained token kept only in `fc_gh` (never in backup files).
- On iOS, deleting the home-screen icon deletes the log. Never advise a reinstall without
  backup → restore → verify.

## Conventions
1. **Bump both markers on every release:** `const BUILD = 'vN'` in `index.html` and `const CACHE = 'fc-vN'`
   in `sw.js`.
2. **Parse-check after every edit:**
   `node -e "const fs=require('fs');new Function(fs.readFileSync('index.html','utf8').match(/<script>([\s\S]*)<\/script>/)[1]);console.log('parse OK')"`
   Any extra `<script>` tag must carry an attribute so this regex still finds the app code.
3. **Preview at phone width before shipping** (`python3 -m http.server 8765`), and seed test data via
   localStorage. Clear it afterwards.
4. **Data shape is append-friendly:** `days[YYYY-MM-DD] = {rules:{meals,charge,bed,adults}, kids:{<id>:{screen(min), short(min), pack, melt, win}}}`;
   `checks[itemId] = date`; `custom[week] = [{id,t}]`; `targets[week] = {budget, short}` (minutes);
   `reviews[week] = {worked, slipped, change, decision, at, kids:{<id>:{happened, next}}}`.
   `commits[<kidId>|'us'] = [{t, from}]` is a history (latest `from` <= date applies; `t:''` ends it);
   the daily tick is `kids[id].commit` / `rules.us`. Checklist item ids are progress keys:
   never rename or reuse them.
5. **Merge never drops data:** restore and GitHub sync use `mergeIn` (phone wins per field, missing
   fields filled). Keep it that way.
6. Colours are CSS tokens with a dark-mode override; never hard-code colours in markup.

## Where the content comes from
The family handoff note kept outside this repo (screen-time plan and parenting advice sections).
