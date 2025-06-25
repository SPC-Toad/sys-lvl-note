# Git Basics for Beginners (with Proper Branching Workflow)

If you're calling yourself a developer, *you must know how to use Git properly*. Here's the minimal workflow that other devs will not hate you.

## Creating a New Branch and Pushing Your Work

```bash
git checkout -b this-is-a-new-branch
```
- Creates a new branch and immediately switches to it.
- Always make a branch for your feature or fix. Never work directly in main.
- Don't actually name your branch `this-is-a-new-branch`... You will make me sad. Very sad 

```bash
git add .
```
- Stages <strong>all</strong> modified and new files in the current directory.
- You can use `git add <filename>` to be more selective if needed.
- <strong>DO NOT</strong> upload sensitive or bulky files like `.env` or an unpacked `node_modules` folder.
- To avoid that, create a `.gitignore` file (if you haven't already). Git will ignore files listed in it when you run `git add .`.


```bash
git commit -m "put your message here"
```
- Commits the staged files with a message describing what you've done.
- Keep it short but meaningful. Don’t just say “update” unless you want to get ignored in code review.

```bash
git push --set-upstream origin this-is-a-new-branch
```
- Pushes your branch to the remote repository (origin).
- You only need `--set-upstream` the first time you push a branch.
- After that, just git push will work.
- Replace `this-is-a-new-branch` with your actual branch name (no {} or quotes).



## Pulling Code / Cloning a Repo
```bash
cd path/to/your/project
```
- Navigate to the project directory in your terminal.
- You can check your current location with `pwd`.

```bash
git clone {link to the project}
```
- Clones a remote repository to your local machine.
- Gives you a full copy including commit history.

```bash
git pull
```
- Pulls the latest changes from the current remote branch into your local branch.
- Always do this before starting new work to avoid merge conflicts.
- Use `git status` to see which branch you're on.

Summary<br/>
✅ Use git checkout -b <branch><br/>
✅ Use git add and git commit -m "msg" with useful messages<br/>
✅ Use git push — from your own branch<br/>
✅ Use git pull often to stay in sync<br/>
❌ Never push directly into main unless...