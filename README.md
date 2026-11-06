# Ship guide

*This is the public copy of the guide. It is published from the private kit repo on every release, so changes made here are overwritten.*

**v0.1.10 — 6 Nov 2026.** For ship-spec 0.9.0 and the mg-ship-kit plugin 0.9.0. The deploy parts (the end of step 5, step 8 and the track chapters) stay thin until the first runs on each track and get rewritten from what they teach. Where this guide and the kit disagree, the kit is right. English is the master; [README.ru.md](README.ru.md) is the Russian edition, made from each English release. This guide describes the kit and never copies its files: for exact columns and commands, follow the links.

New here? Open a new Claude chat, paste this page's link, and ask it to walk you through steps 1 to 6.

## What Ship is

Build, ship and support software people pay for, without a dev team.

You spec one small piece in Claude chat. It lands in the project's Notion tracker as Ready. Claude Code picks it up, builds it, and can't push it to dev until it matches its contract, uses only the agreed stack, leaves no unused code behind and passes a security review. The client tests it on dev; their verdict closes it. Release to main waits until nothing is building and every piece on dev has a verdict.

The rules that hold it together:

- You approve one short screen per piece, the contract. Claude's detailed plan is for Claude, not for you.
- Build small on dev and let the client test there. Whatever nobody uses gets deleted, not hidden. Git keeps it.
- A failed check stops the piece. Fix the check first. No exceptions.
- Every decision reaches you as one real situation, two or three choices, the cost of each in plain words, and a pick. If you don't understand, take the pick.
- Nothing locks until you say yes in your own words. Silence, a question or "unless you object" never counts.
- Any change of direction is flagged in one line: "CHANGE: …".
- Ship's rules live in the kit, not in your project: chat gets them from the ship-spec skill, Claude Code from the plugin. A project's own files hold only what's true of that project, so a kit change reaches every project without anyone copying it.

## 1. Set up once

Per person and per machine. Done once; item 8 says what to repeat after a kit change.

1. **Claude.** Install the Claude desktop app and sign in with your claude.ai account on a plan that includes Claude Code. The Code tab is your workshop; it must be signed in with that same account (not an API key), on Claude Code 2.1.273 or later, because the plugin reaches it from your account. Terminal and VS Code are advanced routes; you don't need them.
2. **GitHub.** An account. The kit repo is private: ask its owner to add you to the mgac-consulting organization (Read is enough). Your claude.ai account reads the kit through the Claude GitHub App, which you give access to ship-kit in item 5.
3. **Notion.** A workspace where you can add teamspaces. In your Claude account, turn the Notion connector on; the Code tab uses it when you sign in with the same account.
4. **On the machine.** git and python3; Node.js if your projects have a frontend. Git access to GitHub from the Code tab: `gh auth login`, then `gh auth setup-git`, or an SSH key. Ask Claude in the Code tab to walk you through it.
5. **The plugin, once.** Ship is one plugin, mg-ship-kit, and you install it once on your claude.ai account. It serves both sides: in chat its ship-spec skill starts project pages and specs pieces; in the Code tab its commands, checks and hooks build and guard them. In claude.ai open **Customize → Plugins → Add → Add marketplace**, enter `mgac-consulting/ship-kit`, connect GitHub and give the Claude GitHub App access to the repo when asked, and turn on **Sync automatically**. Then under **Discover** add **Ship kit**. Install nothing in the Code tab: it gets the plugin from your account at its next start (run `/reload-plugins` if a session is open). From then on every merge to the kit's main reaches chat and Code by itself.
6. **No other copies.** A copy installed in the Code tab with `/plugin` outranks the account copy and doesn't update with it, and an uploaded ship-spec skill loads next to the plugin's. If you have either from an earlier kit, remove it: in the Code tab `/plugin uninstall mg-ship-kit@ship-kit`, then `/plugin marketplace remove ship-kit`; in claude.ai, under **Customize → Skills**, remove ship-spec. `/mg-ship-kit:setup` warns you if a Code copy is still there.
7. **Check.** In a new chat, "what version is ship-spec?" names the version on the kit's main. In the Code tab, `/plugin` lists `mg-ship-kit@synced` and `/mg-ship-kit:setup` is in the command list.
8. **After a kit change.** The kit's README says what changed. Most of it now comes by itself:
   - **The plugin**, in chat and in Code, updates itself from your account. Chat has it within minutes of the merge. Code downloads it when it starts; an open session shows "Plugins changed. Run /reload-plugins to activate."
   - **Each project**, at its next Claude Code session: the plugin opens a PR on the repo that refreshes the GitHub check, and Claude updates the Kit line on the project page. Merge the PR into dev, then pull dev. The PR needs `gh` logged in; without it, Claude says to run `/mg-ship-kit:setup` and commit `.github/` instead.
   - **The project page** gets any new column or line from chat itself, before its next job. It only adds.

   **Once, for 0.7.0:** remove the old copies (item 6) and add the plugin on your account (item 5). Then in each project, open a Claude Code session and run `/mg-ship-kit:setup` once. It takes the old plugin lines out of `.claude/settings.json`, takes the kit's old rule lines out of `CLAUDE.md` (they come from the plugin now) and asks for the Kit line. Commit what it changed. Then in chat say "give me the project instructions" and paste them into the project's Claude Project (step 4, item 5). **Once, for 0.4.0:** the plugin was renamed from `ship` to `mg-ship-kit`, so an update doesn't carry it over. Run `/plugin uninstall ship@ship-kit`, then `/plugin install mg-ship-kit@ship-kit`, then `/reload-plugins`. In each project, run `/mg-ship-kit:setup` and change any `/ship:` command in its `CLAUDE.md` to `/mg-ship-kit:`; setup warns until you do.

## 2. Notion: the project's teamspace

1. In Notion's sidebar, add a teamspace named after the project (for example "Acme CRM"). One teamspace per project.
2. Members: you and your team. Never add a client, and never share a page from it. The client sees the product on dev and live, never this teamspace.
3. Make no pages in it by hand. The project page comes in step 4.

## 3. GitHub: the project's repo

1. Create a private repository named after the project, with a README so it has a first commit. Or, in the Code tab: "create a private GitHub repo named … with branches main and dev, and clone it here".
2. Two branches. `main` is what runs live. `dev` is where pieces land and the client tests. Create `dev` from `main`.
3. Clone the repo to your machine and open its folder in the Code tab.
4. Secrets (passwords, keys, server logins) live in an env file such as `.env` that git ignores. Add `.env` to `.gitignore` before any commit that could carry one; the README-only first commit is fine. Setup adds the full env lines later, and from then on a push that carries an env file is refused, and every push is scanned for a key pasted into code; but a file committed before that stays in git's history.
5. An existing project keeps its repo. Skip item 1 but do items 2 to 4: the two branches, the clone, the env line. Its stack list is read from its dependency files.

## 4. Chat: start the project page

1. In a new Claude chat, say "start a project". The ship-spec skill answers.
2. It lists your teamspaces and asks for what's missing: the project name, one line on what it does, how it's sold (one client, several clients, or a public app), each client with a contact, the repo link, and where secrets are kept. A line each. "Not yet" is fine for the rest.
3. It picks the track: where the project runs, and on what stack. It asks up to four plain questions (a bank, telecom or government client? a devops on your team? mostly data work, or pages and forms? a card for $5 a month or more?) and names the track with its reason: devops, railway or cloudflare. You're never asked which stack. If you want another track and can say why, say so: your track and your reason go on the Track line. A bank, telecom or government client with no devops stops here: find a devops first.
4. It builds the page at the teamspace's root, fetches it back and checks that every part is there. You get the link.
5. It gives you a short block of instructions for the project's Claude Project. Make a Claude Project named after the project and paste the block into its instructions, once. It never needs changing: it tells chat to load ship-spec and holds only the reply style (one screen, one decision, nothing locks without your yes). Everything else comes from the skill, which updates with the kit. Lost it? Say "give me the project instructions".
6. Its last line sends you to `/mg-ship-kit:setup`. Spec nothing yet: set the repo up first (step 5) and pass the checklist (step 6).

The page, top to bottom. The exact lines, columns and views are in [plugins/mg-ship-kit/skills/ship-spec/page-layout.md](https://github.com/mgac-consulting/ship-kit/blob/main/plugins/mg-ship-kit/skills/ship-spec/page-layout.md); this guide never copies them.

| Part | What it holds | Who writes it |
| --- | --- | --- |
| Facts | Fixed facts and links: what it does, how it's sold, the track, clients and contacts, the repo, dev and live links, the Drive folder, the design system, where secrets are kept. Never a secret itself. | Chat at start; you correct it |
| Tracker | One row per piece: name, order, status (Ready → Building → On dev → Done, or Dropped), size, contract file, security (the review's result). The full contract sits inside the row. | Chat adds Ready rows; after pickup only Claude Code |
| Meetings | One row per client meeting, the notes in the body. Ticked once every request is filed. | You; chat ticks it |
| Backlog | One row per request: who said it (the client, your notes on the client, a user, your own idea, a competitor), the exact words, which client asked, status (New → Specced, or Dropped), a link to its piece and that piece's live status. | Chat, from what you give it: meeting notes, an idea, a screenshot, a user's words. You may also add a row by hand |
| Notes | Yours. No tool reads it. | You |

Rules for the whole page: no password, key or server login anywhere on it. Views sort and never filter. Rows are dropped, never deleted.

## 5. Code: set the repo up

1. On your machine, in the Code tab, in the repo folder, run `/mg-ship-kit:setup`. The first setup can't run in a cloud session: the plugin isn't there yet. It asks for the project page link first. Paste it. It reads the track from the Facts. On the cloudflare track it stops here and writes nothing: that track isn't built yet.
2. It finds the backend and frontend folders and writes `ship.json` with the stack list, read from the dependency files. In an empty repo it starts from the kit's default stack (FastAPI, Postgres, React, in `backend/` and `frontend/`), which is the stack of the devops and railway tracks. If your Track line names another stack, say so now; it then starts with an empty list and the right folders.
3. It reads the stack list back: "n Python packages, n Node packages". From now on anything new stops a piece until you approve it in one line.
4. It creates `contracts/` and a `CLAUDE.md` from the kit's template. Fill the placeholders with Claude from what the code shows. `CLAUDE.md` holds only what's true of this project: what it is, its stack, its commands, its own rules. Under 150 lines, no code. Ship's rules are not in it: the plugin adds them to every session, so they update with the plugin. In a `CLAUDE.md` that already exists, setup takes out the lines that copy the kit's old rules and adds one line saying where the rules come from. A kit line you edited by hand stays; Claude shows it and asks you to delete it or keep it as a project rule.
5. It links the tracker on the page you pasted and says "linked the tracker". Then it writes the Kit line in the Facts (`Kit: mg-ship-kit` and the version). Chat checks this line before each job, so a copy that's behind, in chat or in Code, shows at once.
6. It names each tool the check needs, `vulture` for a backend and `knip` for a frontend, as found, MISSING with its install line, or "needed once backend/ exists" in an empty repo. Install a MISSING one now; install a "needed once" one as soon as the first piece makes that folder, before `/mg-ship-kit:cto-check`. The check fails closed without them. It also looks for `gitleaks`, the secret scanner every push needs (D67): install it once on each machine you build on (`brew install gitleaks` on a Mac, or the file from gitleaks' releases page; setup prints the version). Without it every push is refused. The first time gitleaks is there, setup scans the repo's whole history once and lists any key it finds. That list never blocks anything: a key that was ever pushed is exposed, so replace it where it was issued. If it warns that git tracks an env file, take the file out of git as it says and change any password the file held.
7. It writes the GitHub check into `.github/`. From now on GitHub runs the same check on every push to dev and main, and nothing deploys unless it passes; a small secret scan runs on every other branch. Don't edit those files. After a plugin update, the plugin opens a PR that refreshes them at the next session; merge it. It also adds env files to `.gitignore`, and from then on any push that carries one is refused.
8. The plugin comes from your claude.ai account, so the repo declares nothing. If `.claude/settings.json` names the kit from an earlier setup, setup takes those lines out (or deletes the file if nothing else is in it): a copy declared in the repo would outrank your account's and update on its own. If a copy installed with `/plugin` is still on this machine, setup warns and shows how to remove it.
9. Commit the files it added or changed and push them to dev. The push carries no product code, so the gate lets it through. Then open the repo's Actions tab on GitHub: within a few minutes the Ship check on that push turns green. Red or missing: stop and fix it. Every deploy waits for it. The kit's own tests fake GitHub, so your project's first push is the workflow's real test; if it's red, read the run's log before anything else.
10. Connect the deploy. Setup ends with one line for your track; the details are in `.github/ship/README.md` in your repo. Track devops: give your devops that file (section "Your devops"); their deploy starts only after the Ship check passes. Track railway: follow its section "Railway", and ask Claude to walk you through it.
11. When the dev link opens, write it on the Facts' Dev URL line. You don't have to wait for it: spec and build while the deploy is being set up. A piece that reaches dev first waits there for the link and its verdict. The Live URL comes with the first release (step 8).

## 6. Setup checklist: nothing is specced or built until every box is ticked

Ticked by hand for now; the kit has no command for it yet. GitHub doesn't let you tick boxes in a file, so read it as a list, or copy it into the project's Notes.

**Notion**

- [ ] The project has its own teamspace, and only you and your team are in it.
- [ ] The project page sits at the teamspace's root and shows all five parts: Facts, Tracker, Meetings, Backlog, Notes.
- [ ] The Facts name what it does, how it's sold, the track, each client with a contact, the repo, and where secrets are kept.
- [ ] No password, key or server login anywhere on the page.

**Repo**

- [ ] A private repo with `main` and `dev`, and you can push to both from the Code tab.
- [ ] The env file is in `.gitignore` and was never committed.
- [ ] gitleaks is installed, and setup's history scan found no key, or you replaced every key it found.
- [ ] `ship.json`, `contracts/`, `CLAUDE.md`, `.gitignore`, `.claude/settings.json` and `.github/` are committed and pushed to dev.
- [ ] GitHub's Actions tab shows a green Ship check on that push.
- [ ] `CLAUDE.md` is filled, under 150 lines, and holds only this project's facts; its first lines say Ship's rules come from the plugin.
- [ ] The stack list was read back to you and matches what you plan to use.
- [ ] The tracker is linked: the link step said "linked the tracker", or a rerun of setup says "tracker:" with a link.
- [ ] The Facts hold a Kit line naming the plugin's version.
- [ ] Setup named no MISSING tool. A tool "needed once backend/ exists" is fine for now; you install it with the first piece.

**Claude**

- [ ] ship-spec is installed and made for this kit: chat checks it against the Kit line before each job.
- [ ] The project has its own Claude Project, with the instructions block from chat pasted in.
- [ ] The mg-ship-kit plugin is installed in the Code tab and `/mg-ship-kit:next` is in the command list.
- [ ] A cloud session on the repo's dev branch also shows `/mg-ship-kit:next`.
- [ ] The Notion connector is on, and the Code tab can open the project page.

## 7. Build a piece

**Collect requests (chat)**

- After a client meeting, add a row to Meetings with the notes in the body: typed, pasted, or the text of a recording. Then say "file the meeting". Chat lists every request, words copied exactly; you say yes; each becomes a Backlog row.
- Your own idea or a competitor's screen: "add an idea", with the screenshot. A user's review, ticket or message: paste it; it's filed as the user's words, never reworded.
- Nothing is invented. A request that isn't in the Backlog gets added first.

**Spec (chat)**

- Say "spec a piece". Chat shows the New requests; you pick one or more. It drafts the contract on one screen: the words it comes from, what users see, what's out, how to check it ("When I …, then I see …"), and the size as counts of screens, endpoints and tables.
- Read it. Approve in one line, or say what to change. On approval the piece gets a Tracker row as Ready, and its requests become Specced and link to it.
- If one client test can't cover it, split it into pieces, or spec it as a chain (below) to have Claude Code build the parts in one run. Pieces build one at a time.

**Pick up and build (Code tab)**

- Run `/mg-ship-kit:next`. It takes the lowest-Order Ready piece, saves the contract to `contracts/`, marks the row Building and writes a read-only copy into the row. Only Claude Code writes that copy; a hand edit stops it.
- Claude builds what the contract lists and nothing more. You read the one screen, not the plan.
- A change the client asks for mid-build is a contract change: you approve it in one line. Claude never edits a contract on its own.

**Check (Code tab)**

- Run `/mg-ship-kit:cto-check`. Three checks: Size (what got built against the list), Stack (a package or import outside the list), Leftovers (unused code on the lines the piece added, found by vulture and knip). A failed check stops the piece.
- The way out. An extra the client asked for: you approve a contract change in one line. An extra nobody asked for: Claude deletes it. A new package: you approve it in one line, or Claude removes it. Leftovers: Claude deletes them. Then the check runs again.
- On a pass, the same command runs the security review (D66). The script, not Claude, decides whether the piece touches risky ground: routes, the database, login and permissions, uploads, config and env, or a new package. A piece that doesn't gets a skip note. One that does is read by a reviewer agent that sees the diff, the contract and the repo, but never Claude's plan or chat; then a checker agent tries to prove each finding with a test. Only a proven finding counts.
- A proven critical or major finding stops the piece. The way out: Claude fixes it until its test passes (the test stays in the repo), or you waive it in one line with your reason. Claude never waives one. The result lands in `contracts/reviews/` and on the tracker row's Security column, for example "2 found, 1 fixed, 1 waived".
- The review costs tokens: two agents per risky piece. `spend` in `ship.json` picks the models: `lean`, `standard` (the default) or `deep`. Use deep for a bank or telecom project, lean for a side project. `review_all: true` reviews every piece; `review_paths` adds a path the script missed.
- On a pass, Claude may propose a line or two for `CLAUDE.md` from what the piece taught. Approve in one line or decline.

**To dev (Code tab)**

- Every push, to any branch, is first scanned for secrets (D67–D70): gitleaks reads each commit not yet on GitHub, so a key added and later deleted is still found. A finding stops the push before the key leaves your machine. The way out: Claude takes the key out of the code and out of those commits, and you replace it if it was ever pushed anywhere. A false alarm (a test value, an example key) is cleared only by you, in one line; Claude writes it into `.gitleaksignore` with your words. A `gitleaks:allow` comment in the code clears nothing. GitHub scans every branch again, so a push from your own terminal is caught too, though by then the key is on GitHub.
- Say "push". The gate runs the check again, refuses uncommitted changes, and refuses a piece whose review is missing, covers older code, or has a proven critical or major finding still open; GitHub runs the same check once more before anything deploys. The row moves to On dev.
- Ask the client to test on the Dev URL against the "How to check it" lines.
- Start the next piece now with `/mg-ship-kit:next`. Pieces on dev don't block the next one.

**The verdict (Code tab)**

- Accepted: `/mg-ship-kit:done`. The row becomes Done.
- Rejected: `/mg-ship-kit:drop`. The code is deleted and the row becomes Dropped. Git keeps it.
- Nearly right: for now, leave it on dev, file the change as a new request, spec and build the fix; the client then tests both. Whether a reopen joins the kit is decided after the first run.

**A chain: one big feature built in one run**

Some features are one thing to the client and five pieces to build: a list page, a detail page, two endpoints and a table. Piece by piece, that is five specs, five pushes and five rounds of waiting. A chain does it in one run and keeps a reader where it matters.

- **Spec (chat).** Say "spec a chain". The contract is the head: the whole feature's "What users see", the client's test of the whole feature, and a Size that names everything it adds, every item with its full path or name. You approve it in one line, as always. It lands in the tracker as Ready, marked as a chain head.
- **Split (Code tab).** Run `/mg-ship-kit:chain`. Claude picks the head up and splits it into sub-pieces: two to six contracts of its own, foundations first, each naming only what the head names, every head item in exactly one sub-piece, each count equal to its names. The kit refuses any other split, and refuses a head that doesn't name what it counts. Each sub-piece gets a tracker row under the head (the Head column). Then Claude stops and shows you the split on one screen.
- **Go.** Read the sub-pieces, here or in the tracker. Say "go". That is the one stop: after it Claude builds the sub-pieces in order, each through the same check and push as a piece. Before each next sub-piece the kit itself asks GitHub whether the check is green on dev (through `gh`, so `gh auth login` once); not green, or no answer, and the chain halts, however it was resumed.
- **Halts.** The chain stops itself for three reasons only: a check fails for something that needs your word (an extra, a new package); GitHub's check goes red, or can't be asked; a sub-contract leaves a question open (two ways to build it, or something it needs that it doesn't name — Claude never edits a contract to answer its own question). You answer or walk the way out with `/mg-ship-kit:cto-check`, then `/mg-ship-kit:chain` continues from where it stopped. What was built stays where it is.
- **The verdict.** When the last sub-piece lands, the head moves to On dev. The client tests the whole feature against the head's "How to check it" lines and gives one verdict: `/mg-ship-kit:done` on the head closes every sub-piece, and is refused while any sub-piece is still building or being removed. A wrong part is a new piece; the head stays on dev. Release waits for the whole chain, like for any piece on dev.
- **The cost.** The sub-pieces trace to the head, not to the client, and a wrong turn in sub-piece 1 is built into 2 to 5 before anyone looks. The split stop is where you catch it: read the sub-pieces, not the plan.
- **Dropping part of a chain.** A chain nothing was built for drops whole. Once a sub-piece is built, the chain drops sub-piece by sub-piece, the last first; the head follows when the last one lands. Drop one sub-piece and keep the rest, and the head stays put: its contract promises items that won't be built. Either approve a head change in one line that takes them out, or drop the rest.
- A tracker made before chains has no Head column. Chat adds it by itself before its next job on the page.

| Command | Where | What it does |
| --- | --- | --- |
| "start a project", "file the meeting", "add an idea", "spec a piece", "spec a chain", "status", "give me the project instructions" | chat | the ship-spec skill |
| `/mg-ship-kit:setup` | Code tab | once per project; again when the track changes, or when the plugin can't open its refresh PR |
| `/mg-ship-kit:next` | Code tab | pick up the next Ready piece |
| `/mg-ship-kit:cto-check` | Code tab | run the check and the security review, walk the way out, push to dev |
| `/mg-ship-kit:chain` | Code tab | split a chain head, stop for your go, build the sub-pieces in one run |
| `/mg-ship-kit:done` | Code tab | the client accepted it |
| `/mg-ship-kit:drop` | Code tab | the client rejected it: delete the code |

## 8. Release to main

- Release is open when no piece is Building or On dev, so every piece has a verdict. A chain counts as one piece: its head. `/mg-ship-kit:done` tells you when it opens.
- Push dev to main. The gate blocks a release while a piece is building or on dev, and ships only what is already on dev.
- By hand before every release, until the kit covers it: dev and live use separate databases; a backup runs before every migration; automatic daily backups are kept off the server and the latest is under a day old; a restore was tested at least once; rollback is one command and was rehearsed; no secret is in the repo (the scan covers every commit since setup; by hand, check that setup's history list was dealt with); an alert fires when live goes down. The release note opens with the count, for example "Floor: 7/7", then the waivers line `/mg-ship-kit:done` prints when release opens: "Waived: 0", or each waived finding. Nothing ships while one fails. Fix it first.
- Who touches a server. Your own server: you and Claude. A client's cloud server: only after practice runs on your own, in plan mode, every command approved. A bank, telecom or government server: a real devops sets it up and holds access; you build and support. Code reaches live only through the pipeline, never by hand over SSH. Claude never works on a live server in auto mode.

## 9. Support

- Keep the weekly client meeting. Its notes go into Meetings; "file the meeting" turns them into Backlog rows; step 7 takes it from there.
- Bugs and requests sit in the same Backlog, in the client's words. One list.

## 10. Not in the kit yet

Listed so nobody expects it:

- A problem button inside the product that files tickets.
- The server and data checks in the pipeline. Step 8 is by hand.
- The release-time library scan (known holes in the packages you already use). The security review looks at what each piece adds.
- A quick lane. Today a one-line fix needs a small contract too.
- The sales check at the contract (three people who would pay, what they'd pay, who opens the door) and the research method.
- The Claude Design step: a design link in the contract.
- A setup check command. Step 6 is by hand.
- The cloudflare track. Setup stops on it until its stack list, size counts and deploy land.
- Cloud sessions with the plugin. Claude Code on the web may not load plugins from your account, and it never adds a repo's own marketplace. Until a cloud session is tested, build Ship pieces in the desktop Code tab.

For a deep talk, the old roles are still there as chat skills: "Summon PM", "Summon CTO", "Summon Client", "Summon Skeptic". They were written for Scout and still use its words (Build Tracker, Stage 0): use them for thinking, not for Ship's steps.

## Where things live

- This guide: `guide/` in the kit repo. English in [README.md](README.md), Russian in [README.ru.md](README.ru.md). It changes on a branch with the kit, and its header names the kit versions it describes.
- The kit: [mgac-consulting/ship-kit](https://github.com/mgac-consulting/ship-kit). Install and reference: the [kit README](https://github.com/mgac-consulting/ship-kit/blob/main/README.md). The kit repo is private: ask its owner to add you to the mgac-consulting organization (Read is enough).
- Each project: its own Notion teamspace, its own GitHub repo and its own Claude Project. Its facts: the `CLAUDE.md` in that repo and the Facts on its page. Ship's rules: the mg-ship-kit plugin, for chat and Claude Code alike, never copied into a project.
- The old Scout guide was retired on 19 Oct 2026 and is archived in Notion, for history only.
