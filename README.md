# DS 6600 — Lab 0: Setup

**Do this before our first class on Tuesday, August 25.**
Ungraded. Takes about 45 minutes, most of it waiting on downloads.

You do not need any prior experience with Git, GitHub, Docker, or the command line. This module assumes none. If you already use all of these, it'll take you ten minutes — do it anyway, because it registers you in the systems we'll use every class.

There is a **12-minute walkthrough video** on Canvas that does every step below on screen. If you're new to any of this, watch it and follow along rather than reading.

**If you get stuck for more than 15 minutes on any step, stop and email me.** Don't burn a Saturday on an SSH key. Getting stuck here is expected and it's my job, not a reflection on you.

---

## Part 1 — Install four things

Work through these in order. Each has an installer for your operating system.

**1. Docker Desktop** — <https://www.docker.com/get-started/>
Containers. This is by far the largest download, so **start it first** and let it install while you work through the rest. Create a free DockerHub account while you're there; we'll need it in September.

**Important, and it catches everyone:** Docker Desktop has to be *running* for any `docker` command to work. The app isn't just an installer — it runs the engine that actually builds and starts containers, and the command line is only a remote control for it. If Docker Desktop is closed, `docker` commands fail with "Cannot connect to the Docker daemon" or "the system cannot find the file specified."

So: launch Docker Desktop and wait for its status indicator to say it's running before you use `docker` at the command line. It's worth setting it to start automatically when you log in — Settings → General → *Start Docker Desktop when you sign in* — because otherwise you'll rediscover this every time you reboot.

**2. VS Code** — <https://code.visualstudio.com/>
Our editor and terminal for the semester.

**3. Miniconda** — <https://www.anaconda.com/docs/getting-started/miniconda/main>
Manages Python versions and environments. Accept the defaults.

**4. Git** — <https://git-scm.com/downloads>
Version control. On Mac it may already be installed. On Windows, accept the defaults except when it asks about the default editor — choose VS Code if offered.

### Check that they worked

Open a terminal — on Mac, the **Terminal** app; on Windows, **PowerShell** — and type these one at a time:

```
git --version
conda --version
docker --version
docker ps
```

The first three should each print a version number.

`docker ps` is the one that matters. It should print a table header — probably with no rows under it, which is fine and expected. If it instead says it can't connect to the Docker daemon, **Docker Desktop isn't running.** Open the app, wait for it to finish starting, and try again.

Note that `docker --version` will happily print a version even when Docker Desktop is closed, because it's only reporting the command-line tool's own version. That's why we check with `docker ps` too.

If anything says "command not found" or "not recognized," that install didn't finish or didn't get added to your PATH. Note which one and move on; bring it to class or email me.

### Windows only: connect conda to PowerShell

**Skip this if you're on a Mac.**

On Windows, `conda` works out of the box in **Anaconda Prompt** but not in **PowerShell**. We use PowerShell — and so does VS Code's built-in terminal — so this needs fixing once, now.

**1.** Open **Anaconda Prompt** from the Start menu and run:

```
conda init powershell
```

**2.** Now open **PowerShell** and run:

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Answer `Y` if it asks you to confirm. Windows blocks startup scripts by default, which would otherwise silently undo step 1. `-Scope CurrentUser` means you don't need administrator rights.

**3.** Close **every** PowerShell window, then open a new one. You should see `(base)` at the start of the prompt. Confirm with:

```
conda --version
conda activate base
```

If `(base)` shows up, you're done.

**Two things that go wrong:**

- **You reused an open PowerShell window.** These settings only load when PowerShell starts. Close them all and open a fresh one.
- **`conda init` said it worked but there's still no `(base)`.** Your Documents folder may be synced to OneDrive, which can put the startup script somewhere PowerShell doesn't look. Run `$PROFILE` in PowerShell to see the path it expects, and email me what it prints.

**Do not** reinstall Miniconda with the "Add to PATH" option checked. The installer warns against it and it doesn't actually fix this — `conda --version` will work while `conda activate` still fails, which is worse than the current problem because it looks like success.

---

## Part 2 — GitHub account and authentication

**This is the step that gives people trouble.** It's not conceptually hard, it's just fiddly, and it's exactly why we're doing it now instead of in class.

**1. Create a GitHub account** at <https://github.com> if you don't have one. Use whatever username you like — it doesn't need to be your UVA ID. Free tier is fine.

**2. Turn on two-factor authentication.** GitHub requires it. Use **Duo Mobile** — you already have it on your phone for UVA NetBadge, and it stores codes for other services alongside your UVA account. In Duo Mobile, tap **Add**, choose **Use QR code**, and scan the code GitHub shows you.

One thing to expect: this is *not* connected to your UVA login. GitHub won't send you a Duo push the way NetBadge does. When GitHub asks for a code, open Duo Mobile, find the GitHub entry, and type the six digits it displays. (Any other authenticator app works too, if you'd rather keep school and personal accounts separate.)

**3. Authenticate your computer to GitHub.** GitHub no longer accepts your password from the command line, so you need one of these:

- **Easiest:** install **GitHub CLI** from <https://cli.github.com/>, then run `gh auth login` in your terminal and follow the prompts. Choose HTTPS when asked. This handles everything.
- **Alternative:** create a Personal Access Token in GitHub under Settings → Developer settings → Personal access tokens, and use it in place of a password when Git asks.

If the phrase "personal access token" means nothing to you, use the GitHub CLI option. That's what the video does.

---

## Part 3 — Prove it works

Getting one line of text onto GitHub is the whole assignment.

**1. Fork the warmup repository.** Go to <https://github.com/jkropko/ds6600-warmup> and click **Fork** near the top right, then **Create fork**. You now have your own copy at `github.com/YOUR-USERNAME/ds6600-warmup`.

A fork is your own separate copy of someone else's repository. You can change anything in it without affecting mine.

**2. Copy the URL of *your* fork.** On your fork's page, click the green **Code** button and copy the HTTPS URL.

> **Check the URL before you continue.** It must have **your** username in it, not `jkropko`. Copying the URL from my page instead of your fork is the single most common mistake here — everything will look fine until `git push` fails at the end, because you don't have permission to write to my repository.

**3.** In your terminal, navigate to wherever you keep coursework and run:

```
git clone <paste-your-fork-url-here>
cd ds6600-warmup
```

**4.** Open `aboutme.md` in VS Code and fill it in — your name, and a sentence or two about a dataset or topic you find interesting. This is your own copy, so write as much or as little as you like. (It isn't your project topic — we lock that in September. I'm curious, and it gives you something real to commit.)

> **If you haven't used Markdown before, don't worry about it.** A `.md` file is an ordinary text file. Open it, type normally, save. The `#` and `*` characters you'll see in there are formatting marks that GitHub turns into headings and italics when it displays the file, but nothing breaks if you ignore them, and plain sentences work fine. We'll cover Markdown properly when you write your first lab report.

Note that your fork is public, so keep it to whatever you'd be comfortable having visible. First name is fine.

**5.** Save the file, then run these three commands:

```
git add aboutme.md
git commit -m "Fill in aboutme"
git push
```

**6.** Refresh your fork's page on GitHub. Your line should be there.

**That's it — there's nothing to submit.** I can see the list of forks, so if your line is on your fork, you're done.

---

## Part 4 — Request API keys

Four sources, four very different lead times. Start all of them now.

| Source | Where | How long |
|---|---|---|
| **Congress.gov** | <https://api.congress.gov/sign-up/> | Instant |
| **openFEC** | <https://api.data.gov/signup/> | Instant |
| **NewsAPI** | <https://newsapi.org/register> | A day or two |
| **OpenSecrets bulk** | <https://www.opensecrets.org/open-data> | **Possibly weeks — start today** |

OpenSecrets requires registering an account, agreeing to educational-use terms, and then waiting for manual approval. There's no published turnaround. Start it now even though we won't use it until September 17.

**Paste your keys into a plain text file somewhere on your computer for now.** We'll learn the right way to store them on September 15. For the first three weeks we'll do it the wrong way on purpose, and I'll explain why in class.

---

## What "stuck" looks like, and what to do

Most problems are one of these:

**"git: command not found"** — Git installed but isn't on your PATH. Close the terminal, open a new one, try again. If it persists, reinstall and watch for a checkbox about adding to PATH.

**Git asks for a password and rejects the right one** — expected. GitHub stopped accepting account passwords from the command line. Go back to Part 2, step 3.

**`git push` says "Permission denied" or "403"** — you cloned my repository instead of your fork. Run `git remote -v`; if the URL says `jkropko`, that's the problem. Delete the folder, go back to Part 3 step 2, and clone from your own fork's page.

**`git push` says "rejected" or "non-fast-forward"** — the remote has something your computer doesn't. Run `git pull`, then `git push` again.

**"conda: command not recognized" in PowerShell, but it works in Anaconda Prompt** — expected on Windows until you run the two commands in the Windows-only section of Part 1. Worth doing even if Anaconda Prompt feels fine, because VS Code's terminal uses PowerShell and you'll hit this again in September.

**"Cannot connect to the Docker daemon"** — Docker Desktop isn't running. Open the app, wait for it to report that it's started, and run the command again. This is the single most common Docker error and it is never anything more serious than a closed app.

**Docker Desktop won't start on Windows** — usually WSL 2 needs enabling or updating. Docker's error message links to instructions. This one is genuinely annoying and worth emailing me about — and we don't need Docker until September 3, so it's not urgent.

**Something is broken in a way you can't describe** — delete the folder, re-clone, start Part 3 over. Nothing is lost. This is a legitimate fix and you should use it freely, all semester.

---

## One thing to know before Tuesday

You'll type `git add`, `git commit`, and `git push` at the end of every single class this semester. You do not need to understand Git yet. You need those three commands to work, and repetition will handle the rest.

The one idea worth carrying in: **anything you commit, you can get back.** Git isn't paperwork. It's the reason you can experiment aggressively without fear of breaking your own work.

See you on the 25th.
