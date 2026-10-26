# Ship guide

*This is the public copy of the guide. It is published from the private kit repo on every release, so changes made here are overwritten.*

**v0.1.3 — 26 Oct 2026.** For ship-spec 0.4.1 and the ship plugin 0.3.2. The deploy parts (the end of step 5, step 8 and the track chapters) stay thin until the first runs on each track and get rewritten from what they teach. Where this guide and the kit disagree, the kit is right. English is the master; [README.ru.md](README.ru.md) is the Russian edition, made from each English release. This guide describes the kit and never copies its files: for exact columns and commands, follow the links.

New here? Open a new Claude chat, paste this page's link, and ask it to walk you through steps 1 to 6.

## What Ship is

Build, ship and support software people pay for, without a dev team.

You spec one small piece in Claude chat. It lands in the project's Notion tracker as Ready. Claude Code picks it up, builds it, and can't push it to dev until it matches its contract, uses only the agreed stack and leaves no unused code behind. The client tests it on dev; their verdict closes it. Release to main waits until nothing is building and every piece on dev has a verdict.

The rules that hold it together:

- You approve one short screen per piece, the contract. Claude's detailed plan is for Claude, not for you.
- Build small on dev and let the client test there. Whatever nobody uses gets deleted, not hidden. Git keeps it.
- A failed check stops the piece. Fix the check first. No exceptions.
- Every decision reaches you as one real situation, two or three choices, the cost of each in plain words, and a pick. If you don't understand, take the pick.
- Nothing locks until you say yes in your own words. Silence, a question or "unless you object" never counts.
- Any change of direction is flagged in one line: "CHANGE: …".

## 1. Set up once

Per person and per machine. Done once; item 8 says what to repeat after a kit change.

1. **Claude.** Install the Claude desktop app and sign in with a plan that includes Claude Code. The Code tab is your workshop. Terminal and VS Code are advanced routes; you don't need them.
2. **GitHub.** An account. The kit repo is private: ask its owner to add you to the mgac-consulting organization (Read is enough).
3. **Notion.** A workspace where you can add teamspaces. In your Claude account, turn the Notion connector on; the Code tab uses it when you sign in with the same account.
4. **On the machine.** git and python3; Node.js if your projects have a frontend. Git access to GitHub from the Code tab: `gh auth login`, then `gh auth setup-git`, or an SSH key. Ask Claude in the Code tab to walk you through it.
5. **The chat skill.** Get the `ship-spec` zip from the kit's owner, or zip the kit's [`chat/ship-spec`](https://github.com/mgac-consulting/ship-kit/blob/main/chat/ship-spec) folder yourself (both files). Upload it as a skill in your Claude account. Its Version line tells chat which version is installed. Repeat after each kit change.
6. **The plugin.** In the Code tab run `/plugin marketplace add mgac-consulting/ship-kit`, then `/plugin install ship@ship-kit`. The desktop app's Code tab installs plugins the same way from the `+` menu. Plugins don't load in cloud sessions.
7. **Check.** In a new chat, "what can ship-spec do?" gets an answer. In the Code tab, `/ship:setup` is in the command list.
8. **After a kit change.** The kit's README says what changed. If `chat/` changed, repeat item 5. If `plugins/` changed: in the Code tab, `/plugin marketplace update ship-kit`, then in `/plugin` open Installed → ship → Update now, then `/reload-plugins`; then run `/ship:setup` again in each project and commit what it rewrote.

## 2. Notion: the project's teamspace

1. In Notion's sidebar, add a teamspace named after the project (for example "Acme CRM"). One teamspace per project.
2. Members: you and your team. Never add a client, and never share a page from it. The client sees the product on dev and live, never this teamspace.
3. Make no pages in it by hand. The project page comes in step 4.

## 3. GitHub: the project's repo

1. Create a private repository named after the project, with a README so it has a first commit. Or, in the Code tab: "create a private GitHub repo named … with branches main and dev, and clone it here".
2. Two branches. `main` is what runs live. `dev` is where pieces land and the client tests. Create `dev` from `main`.
3. Clone the repo to your machine and open its folder in the Code tab.
4. Secrets (passwords, keys, server logins) live in an env file such as `.env` that git ignores. Add `.env` to `.gitignore` before any commit that could carry one; the README-only first commit is fine. Setup adds the full env lines later, and from then on a push that carries an env file is refused; but a file committed before that stays in git's history.
5. An existing project keeps its repo. Skip item 1 but do items 2 to 4: the two branches, the clone, the env line. Its stack list is read from its dependency files.

## 4. Chat: start the project page

1. In a new Claude chat, say "start a project". The ship-spec skill answers.
2. It lists your teamspaces and asks for what's missing: the project name, one line on what it does, how it's sold (one client, several clients, or a public app), each client with a contact, the repo link, and where secrets are kept. A line each. "Not yet" is fine for the rest.
3. It picks the track: where the project runs, and on what stack. It asks up to four plain questions (a bank, telecom or government client? a devops on your team? mostly data work, or pages and forms? a card for $5 a month or more?) and names the track with its reason: devops, railway or cloudflare. You're never asked which stack. If you want another track and can say why, say so: your track and your reason go on the Track line. A bank, telecom or government client with no devops stops here: find a devops first.
4. It builds the page at the teamspace's root, fetches it back and checks that every part is there. You get the link.
5. Its last line sends you to `/ship:setup`. Spec nothing yet: set the repo up first (step 5) and pass the checklist (step 6).

The page, top to bottom. The exact lines, columns and views are in [chat/ship-spec/page-layout.md](https://github.com/mgac-consulting/ship-kit/blob/main/chat/ship-spec/page-layout.md); this guide never copies them.

| Part | What it holds | Who writes it |
| --- | --- | --- |
| Facts | Fixed facts and links: what it does, how it's sold, the track, clients and contacts, the repo, dev and live links, the Drive folder, the design system, where secrets are kept. Never a secret itself. | Chat at start; you correct it |
| Tracker | One row per piece: name, order, status (Ready → Building → On dev → Done, or Dropped), size, contract file. The full contract sits inside the row. | Chat adds Ready rows; after pickup only Claude Code |
| Meetings | One row per client meeting, the notes in the body. Ticked once every request is filed. | You; chat ticks it |
| Backlog | One row per request: who said it (the client, your notes on the client, a user, your own idea, a competitor), the exact words, which client asked, status (New → Specced, or Dropped), a link to its piece and that piece's live status. | Chat, from what you give it: meeting notes, an idea, a screenshot, a user's words. You may also add a row by hand |
| Notes | Yours. No tool reads it. | You |

Rules for the whole page: no password, key or server login anywhere on it. Views sort and never filter. Rows are dropped, never deleted.

## 5. Code: set the repo up

1. In the Code tab, in the repo folder, run `/ship:setup`. It asks for the project page link first. Paste it. It reads the track from the Facts. On the cloudflare track it stops here and writes nothing: that track isn't built yet.
2. It finds the backend and frontend folders and writes `ship.json` with the stack list, read from the dependency files. In an empty repo it starts from the kit's default stack (FastAPI, Postgres, React, in `backend/` and `frontend/`), which is the stack of the devops and railway tracks. If your Track line names another stack, say so now; it then starts with an empty list and the right folders.
3. It reads the stack list back: "n Python packages, n Node packages". From now on anything new stops a piece until you approve it in one line.
4. It creates `contracts/` and a `CLAUDE.md` from the kit's template. Fill the placeholders with Claude from what the code shows; keep every rule line and add none. Rules and stack only, no code, under 150 lines. A `CLAUDE.md` that already exists is left alone; Claude says in one line what the template has that it lacks.
5. It links the tracker on the page you pasted and says "linked the tracker".
6. It names each tool the check needs, `vulture` for a backend and `knip` for a frontend, as found, MISSING with its install line, or "needed once backend/ exists" in an empty repo. Install a MISSING one now; install a "needed once" one as soon as the first piece makes that folder, before `/ship:cto-check`. The check fails closed without them. If it warns that git tracks an env file, take the file out of git as it says and change any password the file held.
7. It writes the GitHub check into `.github/`. From now on GitHub runs the same check on every push to dev and main, and nothing deploys unless it passes. Don't edit those files; after a plugin update, run `/ship:setup` again to refresh them. It also adds env files to `.gitignore`, and from then on any push that carries one is refused.
8. Commit the files it added or changed and push them to dev. The push carries no product code, so the gate lets it through. Then open the repo's Actions tab on GitHub: within a few minutes the Ship check on that push turns green. Red or missing: stop and fix it. Every deploy waits for it. The kit's own tests fake GitHub, so your project's first push is the workflow's real test; if it's red, read the run's log before anything else.
9. Connect the deploy. Setup ends with one line for your track; the details are in `.github/ship/README.md` in your repo. Track devops: give your devops that file (section "Your devops"); their deploy starts only after the Ship check passes. Track railway: follow its section "Railway", and ask Claude to walk you through it.
10. When the dev link opens, write it on the Facts' Dev URL line. You don't have to wait for it: spec and build while the deploy is being set up. A piece that reaches dev first waits there for the link and its verdict. The Live URL comes with the first release (step 8).

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
- [ ] `ship.json`, `contracts/`, `CLAUDE.md`, `.gitignore` and `.github/` are committed and pushed to dev.
- [ ] GitHub's Actions tab shows a green Ship check on that push.
- [ ] `CLAUDE.md` is filled, under 150 lines, rules and stack only.
- [ ] The stack list was read back to you and matches what you plan to use.
- [ ] The tracker is linked: the link step said "linked the tracker", or a rerun of setup says "tracker:" with a link.
- [ ] Setup named no MISSING tool. A tool "needed once backend/ exists" is fine for now; you install it with the first piece.

**Claude**

- [ ] ship-spec is installed and its Version line matches the kit. Chat says so when asked.
- [ ] The ship plugin is installed in the Code tab and `/ship:next` is in the command list.
- [ ] The Notion connector is on, and the Code tab can open the project page.

## 7. Build a piece

**Collect requests (chat)**

- After a client meeting, add a row to Meetings with the notes in the body: typed, pasted, or the text of a recording. Then say "file the meeting". Chat lists every request, words copied exactly; you say yes; each becomes a Backlog row.
- Your own idea or a competitor's screen: "add an idea", with the screenshot. A user's review, ticket or message: paste it; it's filed as the user's words, never reworded.
- Nothing is invented. A request that isn't in the Backlog gets added first.

**Spec (chat)**

- Say "spec a piece". Chat shows the New requests; you pick one or more. It drafts the contract on one screen: the words it comes from, what users see, what's out, how to check it ("When I …, then I see …"), and the size as counts of screens, endpoints and tables.
- Read it. Approve in one line, or say what to change. On approval the piece gets a Tracker row as Ready, and its requests become Specced and link to it.
- If one client test can't cover it, split it. Pieces build one at a time.

**Pick up and build (Code tab)**

- Run `/ship:next`. It takes the lowest-Order Ready piece, saves the contract to `contracts/`, marks the row Building and writes a read-only copy into the row. Only Claude Code writes that copy; a hand edit stops it.
- Claude builds what the contract lists and nothing more. You read the one screen, not the plan.
- A change the client asks for mid-build is a contract change: you approve it in one line. Claude never edits a contract on its own.

**Check (Code tab)**

- Run `/ship:cto-check`. Three checks: Size (what got built against the list), Stack (a package or import outside the list), Leftovers (unused code on the lines the piece added, found by vulture and knip). A failed check stops the piece.
- The way out. An extra the client asked for: you approve a contract change in one line. An extra nobody asked for: Claude deletes it. A new package: you approve it in one line, or Claude removes it. Leftovers: Claude deletes them. Then the check runs again.
- On a pass, Claude may propose a line or two for `CLAUDE.md` from what the piece taught. Approve in one line or decline.

**To dev (Code tab)**

- Say "push". The gate runs the check again and refuses uncommitted changes; GitHub runs it once more before anything deploys. The row moves to On dev.
- Ask the client to test on the Dev URL against the "How to check it" lines.
- Start the next piece now with `/ship:next`. Pieces on dev don't block the next one.

**The verdict (Code tab)**

- Accepted: `/ship:done`. The row becomes Done.
- Rejected: `/ship:drop`. The code is deleted and the row becomes Dropped. Git keeps it.
- Nearly right: for now, leave it on dev, file the change as a new request, spec and build the fix; the client then tests both. Whether a reopen joins the kit is decided after the first run.

| Command | Where | What it does |
| --- | --- | --- |
| "start a project", "file the meeting", "add an idea", "spec a piece", "status" | chat | the ship-spec skill |
| `/ship:setup` | Code tab | once per project, and again after a plugin update |
| `/ship:next` | Code tab | pick up the next Ready piece |
| `/ship:cto-check` | Code tab | run the check, walk the way out, push to dev |
| `/ship:done` | Code tab | the client accepted it |
| `/ship:drop` | Code tab | the client rejected it: delete the code |

## 8. Release to main

- Release is open when no piece is Building or On dev, so every piece has a verdict. `/ship:done` tells you when it opens.
- Push dev to main. The gate blocks a release while a piece is building or on dev, and ships only what is already on dev.
- By hand before every release, until the kit covers it: dev and live use separate databases; a backup runs before every migration; automatic daily backups are kept off the server and the latest is under a day old; a restore was tested at least once; rollback is one command and was rehearsed; no secret is in the repo; an alert fires when live goes down. The release note opens with the count, for example "Floor: 7/7". Nothing ships while one fails. Fix it first.
- Who touches a server. Your own server: you and Claude. A client's cloud server: only after practice runs on your own, in plan mode, every command approved. A bank, telecom or government server: a real devops sets it up and holds access; you build and support. Code reaches live only through the pipeline, never by hand over SSH. Claude never works on a live server in auto mode.

## 9. Support

- Keep the weekly client meeting. Its notes go into Meetings; "file the meeting" turns them into Backlog rows; step 7 takes it from there.
- Bugs and requests sit in the same Backlog, in the client's words. One list.

## 10. Not in the kit yet

Listed so nobody expects it:

- A problem button inside the product that files tickets.
- The server and data checks in the pipeline. Step 8 is by hand.
- The code-safety scan (missing login, user text going into SQL, keys in code) and the release-time library scan.
- A quick lane. Today a one-line fix needs a small contract too.
- The sales check at the contract (three people who would pay, what they'd pay, who opens the door) and the research method.
- The Claude Design step: a design link in the contract.
- A setup check command. Step 6 is by hand.
- The cloudflare track. Setup stops on it until its stack list, size counts and deploy land.

For a deep talk, the old roles are still there as chat skills: "Summon PM", "Summon CTO", "Summon Client", "Summon Skeptic". They were written for Scout and still use its words (Build Tracker, Stage 0): use them for thinking, not for Ship's steps.

## Where things live

- This guide: `guide/` in the kit repo. English in [README.md](README.md), Russian in [README.ru.md](README.ru.md). It changes on a branch with the kit, and its header names the kit versions it describes.
- The kit: [mgac-consulting/ship-kit](https://github.com/mgac-consulting/ship-kit). Install and reference: the [kit README](https://github.com/mgac-consulting/ship-kit/blob/main/README.md). The kit repo is private: ask its owner to add you to the mgac-consulting organization (Read is enough).
- Each project: its own Notion teamspace and its own GitHub repo. Its rules: the `CLAUDE.md` in that repo. No Notion copies.
- The old Scout guide was retired on 19 Oct 2026 and is archived in Notion, for history only.
