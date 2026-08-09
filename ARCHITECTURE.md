# Thirsty Machines — content architecture handoff

Written for a developer taking the current static site and making it
extensible. The live site is unchanged by this document; nothing here has
been implemented yet.

## Where things stand

The site is four static files (`index.html`, `add-up-to.html`,
`about.html`, `support.js`), deployed from GitHub to Vercel with no build
step. Every story's text, its node shape and colour, its position on the
map and its connectors are written directly into `index.html`.

Adding a thirteenth story today means editing that file by hand.

## Target: file-based stories

Each story becomes its own file in `/stories`, e.g.
`/stories/mason-county.md` (front-matter + body) or
`/stories/mason-county.json`. One file holds:

| field | notes |
| --- | --- |
| `id` | slug, used by the deep link (`/#mason`) |
| `title` | the node label on the map, e.g. "No Name Given" |
| `place` | the eyebrow line, e.g. "mason county" |
| `category` | `location` · `organisation` · `publication` · `both` — drives the dot shape and colour |
| `body` | passage text, with redaction marks inline (see below) |
| `question` | the closing question |
| `source` | link, plus publisher and date |
| `links` | ids of other stories this one connects to, each with its short connector label |
| `map` | `x`, `y` on the 1260 × 560 canvas, and the label offset |

The page reads every file in the folder and renders the map and the story
overlays from whatever is there. No story text in a component.

**Redaction marks.** The blacked-out spans are part of the copy, so they
belong in the story file, not the renderer. A simple inline convention —
`[[unnamed buyers]]` — keeps the file readable and lets the renderer emit
the existing bar markup.

## Discovery: keep it simple

Static hosting can't list a directory, so a lone new file isn't
discoverable on its own. Two options; take the first unless a build step
already exists for another reason.

1. **`/stories/index.json`** — an ordered array of filenames. Adding a
   story is two edits: the new file, and one line in the index. It is
   plain, visible in a diff, and doubles as the ordering control.
2. **A build step** — a small script (or a framework's file-based content
   loader) globs `/stories/*` at build time and emits the manifest.
   Genuinely one file per story, at the cost of a build to maintain.

Either way Vercel redeploys on push, so publishing stays: commit, done.

## Map positions

Open question — the twelve nodes and seventeen connectors are hand-placed,
and auto-placement would undo that. The site's creator wants to be asked
per story rather than have new ones fall wherever an algorithm puts them.
So: `map.x` / `map.y` are required fields, chosen by hand when a story is
written up. A story file without coordinates should fail loudly at build
rather than render somewhere arbitrary.

## Two ways to add a file

1. **GitHub web interface** — open `/stories`, "Add file", paste, commit.
   Zero setup, but means writing the format by hand each time.
2. **A Git-based CMS, e.g. Decap** (free, built for GitHub + static sites)
   — a form with a title field, text areas and a category dropdown, which
   writes the file to GitHub for you. Same auto-deploy. Worth the one-time
   setup if stories go up regularly.

Recommend the second if the collection keeps growing; the first works from
day one either way.

## Submissions stay separate

Visitors submit links through an external Google Form, linked from the
legend bar and the About page, opening in a new tab. It writes nothing to
the repository. Responses are reviewed by hand, and anything worth
including is written up as a story file by the process above.

Not to be built: an in-site form wired to the repo, automated moderation,
AI-drafted stories from submitted links, a public queue of pending
submissions. Every publishing decision is made by a person.
