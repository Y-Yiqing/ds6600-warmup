# DS 6600 — Lab 0: Setup

**Do this before our first class on Tuesday, August 25.**
Ungraded. Takes about 45 minutes, most of it waiting on downloads.

You do not need any prior experience with Git, GitHub, Docker, or the command line. This module assumes none. If you already use all of these, it'll take you ten minutes — do it anyway, because it registers you in the systems we'll use every class.

There is a **12-minute walkthrough video** on Canvas that does every step below on screen. If you're new to any of this, watch it and follow along rather than reading.

**If you get stuck for more than 15 minutes on any step, stop and email me.** Don't burn a Saturday on an SSH key. Getting stuck here is expected and it's my job, not a reflection on you.

---

## Part 1 — Install four things

Work through these in order. Each has an installer for your operating system.

**1. VS Code** — <https://code.visualstudio.com/>
Our editor and terminal for the semester.

**2. Miniconda** — <https://www.anaconda.com/docs/getting-started/miniconda/main>
Manages Python versions and environments. Accept the defaults.

**3. Git** — <https://git-scm.com/downloads>
Version control. On Mac it may already be installed. On Windows, accept the defaults except when it asks about the default editor — choose VS Code if offered.

**4. Docker Desktop** — <https://www.docker.com/get-started/>
Containers. Create a free DockerHub account while you're there; we'll need it in September. This is the largest download; start it first and let it run while you do everything else.

### Check that they worked

Open a terminal — on Mac, the **Terminal** app; on Windows, **PowerShell** — and type these one at a time:

```
git --version
conda --version
docker --version
```

Each should print a version number. If any says "command not found" or "not recognized," that install didn't finish or didn't get added to your PATH. Note which one and move on; bring it to class or email me.

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

**2. Turn on two-factor authentication.** GitHub requires it. Use your phone's authenticator app.

**3. Authenticate your computer to GitHub.** GitHub no longer accepts your password from the command line, so you need one of these:

- **Easiest:** install **GitHub CLI** from <https://cli.github.com/>, then run `gh auth login` in your terminal and follow the prompts. Choose HTTPS when asked. This handles everything.
- **Alternative:** create a Personal Access Token in GitHub under Settings → Developer settings → Personal access tokens, and use it in place of a password when Git asks.

If the phrase "personal access token" means nothing to you, use the GitHub CLI option. That's what the video does.

---

## Part 3 — Prove it works

I've set up a warmup repository. Getting one line of text into it is the whole assignment.

**1.** Open the assignment link on Canvas. It creates a repository for you called `ds6600-warmup-yourusername`.

**2.** Copy the repository URL from the green **Code** button on GitHub.

**3.** In your terminal, navigate to wherever you keep coursework and run:

```
git clone <paste-the-url-here>
cd ds6600-warmup-yourusername
```

**4.** Open `roster.md` in VS Code. Add one line with your name and one sentence about a dataset you find interesting. (This isn't your project topic — we lock that in September. I'm just curious, and it gives you something real to commit.)

**5.** Save the file, then run these three commands:

```
git add roster.md
git commit -m "Add my line to the roster"
git push
```

**6.** Refresh the repository page on GitHub. Your line should be there.

Within a minute or two, an automated check will comment on your commit confirming it worked. **That comment is your confirmation the assignment is complete** — there's nothing to submit on Canvas.

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

**`git push` says "rejected" or "non-fast-forward"** — the remote has something your computer doesn't. Run `git pull`, then `git push` again.

**"conda: command not recognized" in PowerShell, but it works in Anaconda Prompt** — expected on Windows until you run the two commands in the Windows-only section of Part 1. Worth doing even if Anaconda Prompt feels fine, because VS Code's terminal uses PowerShell and you'll hit this again in September.

**Docker Desktop won't start on Windows** — usually WSL 2 needs enabling or updating. Docker's error message links to instructions. This one is genuinely annoying and worth emailing me about — and we don't need Docker until September 3, so it's not urgent.

**Something is broken in a way you can't describe** — delete the folder, re-clone, start Part 3 over. Nothing is lost. This is a legitimate fix and you should use it freely, all semester.

---

## One thing to know before Tuesday

You'll type `git add`, `git commit`, and `git push` at the end of every single class this semester. You do not need to understand Git yet. You need those three commands to work, and repetition will handle the rest.

The one idea worth carrying in: **anything you commit, you can get back.** Git isn't paperwork. It's the reason you can experiment aggressively without fear of breaking your own work.

See you on the 25th.# DS 6600 — Lab 0: Setup

**Do this before our first class on Tuesday, August 25.**
Ungraded. Takes about 45 minutes, most of it waiting on downloads.

You do not need any prior experience with Git, GitHub, Docker, or the command line. This module assumes none. If you already use all of these, it'll take you ten minutes — do it anyway, because it registers you in the systems we'll use every class.

There is a **12-minute walkthrough video** on Canvas that does every step below on screen. If you're new to any of this, watch it and follow along rather than reading.

**If you get stuck for more than 15 minutes on any step, stop and email me.** Don't burn a Saturday on an SSH key. Getting stuck here is expected and it's my job, not a reflection on you.

---

## Part 1 — Install four things

Work through these in order. Each has an installer for your operating system.

**1. VS Code** — <https://code.visualstudio.com/>
Our editor and terminal for the semester.

**2. Miniconda** — <https://www.anaconda.com/docs/getting-started/miniconda/main>
Manages Python versions and environments. Accept the defaults.

**3. Git** — <https://git-scm.com/downloads>
Version control. On Mac it may already be installed. On Windows, accept the defaults except when it asks about the default editor — choose VS Code if offered.

**4. Docker Desktop** — <https://www.docker.com/get-started/>
Containers. Create a free DockerHub account while you're there; we'll need it in September. This is the largest download; start it first and let it run while you do everything else.

### Check that they worked

Open a terminal — on Mac, the **Terminal** app; on Windows, **PowerShell** — and type these one at a time:

```
git --version
conda --version
docker --version
```

Each should print a version number. If any says "command not found" or "not recognized," that install didn't finish or didn't get added to your PATH. Note which one and move on; bring it to class or email me.

---

## Part 2 — GitHub account and authentication

**This is the step that gives people trouble.** It's not conceptually hard, it's just fiddly, and it's exactly why we're doing it now instead of in class.

**1. Create a GitHub account** at <https://github.com> if you don't have one. Use whatever username you like — it doesn't need to be your UVA ID. Free tier is fine.

**2. Turn on two-factor authentication.** GitHub requires it. Use your phone's authenticator app.

**3. Authenticate your computer to GitHub.** GitHub no longer accepts your password from the command line, so you need one of these:

- **Easiest:** install **GitHub CLI** from <https://cli.github.com/>, then run `gh auth login` in your terminal and follow the prompts. Choose HTTPS when asked. This handles everything.
- **Alternative:** create a Personal Access Token in GitHub under Settings → Developer settings → Personal access tokens, and use it in place of a password when Git asks.

If the phrase "personal access token" means nothing to you, use the GitHub CLI option. That's what the video does.

---

## Part 3 — Prove it works

I've set up a warmup repository. Getting one line of text into it is the whole assignment.

**1.** Open the assignment link on Canvas. It creates a repository for you called `ds6600-warmup-yourusername`.

**2.** Copy the repository URL from the green **Code** button on GitHub.

**3.** In your terminal, navigate to wherever you keep coursework and run:

```
git clone <paste-the-url-here>
cd ds6600-warmup-yourusername
```

**4.** Open `roster.md` in VS Code. Add one line with your name and one sentence about a dataset you find interesting. (This isn't your project topic — we lock that in September. I'm just curious, and it gives you something real to commit.)

**5.** Save the file, then run these three commands:

```
git add roster.md
git commit -m "Add my line to the roster"
git push
```

**6.** Refresh the repository page on GitHub. Your line should be there.

Within a minute or two, an automated check will comment on your commit confirming it worked. **That comment is your confirmation the assignment is complete** — there's nothing to submit on Canvas.

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

**`git push` says "rejected" or "non-fast-forward"** — the remote has something your computer doesn't. Run `git pull`, then `git push` again.

**Docker Desktop won't start on Windows** — usually WSL 2 needs enabling or updating. Docker's error message links to instructions. This one is genuinely annoying and worth emailing me about — and we don't need Docker until September 3, so it's not urgent.

**Something is broken in a way you can't describe** — delete the folder, re-clone, start Part 3 over. Nothing is lost. This is a legitimate fix and you should use it freely, all semester.

---

## One thing to know before Tuesday

You'll type `git add`, `git commit`, and `git push` at the end of every single class this semester. You do not need to understand Git yet. You need those three commands to work, and repetition will handle the rest.

The one idea worth carrying in: **anything you commit, you can get back.** Git isn't paperwork. It's the reason you can experiment aggressively without fear of breaking your own work.

See you on the 25th.
