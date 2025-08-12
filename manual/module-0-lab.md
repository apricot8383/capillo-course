
# Module 0 — Setup & GitHub (macOS, Beginner)

This manual is for absolute beginners. Follow the steps exactly (one block at a time). After each “Checkpoint,” you should see the expected result.

---

## 1) What are Git & GitHub (and why)?
- **Git**: a “time machine” for files. You make snapshots called **commits** so you can go back.
- **GitHub**: a website that stores your Git repos in the cloud and runs your robot helper (**Actions/CI**) to check your code automatically.

## 2) What we are building
1) **capillo-course** (Public): “course notebook” — manuals, checklists, templates.  
2) **capillo-template-python** (Public, Template): a reusable starter with structure + security checks.  
3) **capillo-logtool** (Public): a real project created from the template (portfolio piece).

---

## 3) Terminal safety rules
- Paste **one block at a time**.
- Don’t paste lines that start with `#` (those are comments).
- If you see `heredoc>` or `dquote>` → press **Ctrl+C** and retry.
- You’re using **zsh** (default on macOS).

---

## 4) One-time: connect your Mac to GitHub with SSH

### 4.1 Create an SSH key (your secure “door key”)
```bash
ssh-keygen -t ed25519 -C "YOUR_EMAIL" -f ~/.ssh/id_ed25519


What it does
ssh-keygen creates a key pair:
Private key: ~/.ssh/id_ed25519 (keep secret)
Public key: ~/.ssh/id_ed25519.pub (share with GitHub)
-t ed25519 → modern secure key type
-C "YOUR_EMAIL" → adds a label so you know what the key is for
-f ... → exact save location
4.2 Start the agent & load your key (so you don’t retype the passphrase)
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_ed25519 || ssh-add -K ~/.ssh/id_ed25519
What it does
ssh-agent runs in the background and keeps your key unlocked.
ssh-add adds your key and stores the passphrase in Keychain.
4.3 Add the public key to GitHub
pbcopy < ~/.ssh/id_ed25519.pub
What it does
Copies your public key to the clipboard.
Then: GitHub → Settings → SSH and GPG keys → New SSH key → paste → Save.
4.4 Test the connection
ssh -T git@github.com
Success looks like
Hi <your-username>! You've successfully authenticated, but GitHub does not provide shell access.
4.5 Make SSH the default for GitHub
cat > ~/.ssh/config <<'EOC'
Host github.com
  HostName github.com
  User git
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
EOC
chmod 600 ~/.ssh/config
What it does
Tells macOS to always use your key for github.com.
Checkpoint: ssh -T git@github.com shows the “Hi … no shell access” message ✅
5) Create your “course notebook” repo — capillo-course
5.1 Create on GitHub (web)
New → Repository
Name: capillo-course (Public)
Add: README, MIT License, Python .gitignore → Create
5.2 Clone and scaffold
mkdir -p ~/capillo/course
cd ~/capillo/course
git clone git@github.com:<USER>/capillo-course.git
cd capillo-course

mkdir -p manual rubrics templates
printf "# Capillo Course\n\nThis repo tracks the entire program.\n" > README.md

git add .
git commit -m "chore: init course repo with manual/rubrics/templates"
git push
What it does
mkdir -p → creates folders if missing.
git clone → downloads your GitHub repo locally.
git add . → stage everything.
git commit -m → take a named snapshot.
git push → send snapshot to GitHub.
Checkpoint: On GitHub, you see manual/, rubrics/, templates/, and README ✅
6) Build a reusable template — capillo-template-python
6.1 Create on GitHub (web)
New → Repository → capillo-template-python (Public) → Create
6.2 Clone and scaffold
mkdir -p ~/capillo/templates
cd ~/capillo/templates
git clone git@github.com:<USER>/capillo-template-python.git
cd capillo-template-python
mkdir -p src/project tests docs/daily docs/screens examples .github/workflows
printf "[tool.poetry]\nname = \"capillo-template-python\"\nversion = \"0.1.0\"\n" > pyproject.toml
printf "# <Project Name>\n\nProblem → Approach → How to run → Testing → Metrics → Artifacts\n" > README.md
printf "__pycache__/\n*.pyc\n.venv/\n.env\ndist/\n" > .gitignore
Why these folders
src/project → your Python package code (rename per-project later)
tests → unit tests
docs/examples → notes and sample usage
.github/workflows → CI workflows
pyproject.toml → minimal project metadata
.gitignore → ignore caches, venv, build output
6.3 Add the Security CI workflow (robot helper)
cat > .github/workflows/security.yml <<'EOW'
name: Security CI
on: [push, pull_request]
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install poetry ruff mypy bandit pip-audit pytest
      - run: poetry --version || true
      - run: ruff check .
      - run: pytest -q || true
      - run: mypy src || true
      - run: bandit -q -r src || true
      - run: pip-audit || true
EOW

git add .
git commit -m "feat: bootstrap template with security CI"
git push
What it does
Runs on every push/PR:
ruff (code style), pytest (tests), mypy (types),
bandit (security static analysis), pip-audit (vuln scan).
6.4 Mark as Template + enable security (web)
Settings → General → check Template repository
Settings → Security → enable Dependabot alerts + Secret scanning
Checkpoint: Actions shows the workflow running (yellow → green) ✅
7) Create your first project — capillo-logtool (from template)
7.1 Create from template (web)
Open capillo-template-python → Use this template → new repo: capillo-logtool (Public)
7.2 Clone and scaffold
mkdir -p ~/capillo/projects
cd ~/capillo/projects
git clone git@github.com:<USER>/capillo-logtool.git
cd capillo-logtool

mkdir -p src/logtool tests docs/daily docs/screens examples .github/workflows
printf "__version__ = '0.1.0'\n" > src/logtool/__init__.py
printf "def test_smoke():\n    assert 1 + 1 == 2\n" > tests/test_smoke.py
7.3 Add the same CI to this project
cat > .github/workflows/security.yml <<'EOW'
name: Security CI
on: [push, pull_request]
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install poetry ruff mypy bandit pip-audit pytest
      - run: poetry --version || true
      - run: ruff check .
      - run: pytest -q || true
      - run: mypy src || true
      - run: bandit -q -r src || true
      - run: pip-audit || true
EOW

git add .
git commit -m "chore: scaffold package, tests, and security CI"
git push
Checkpoint: Actions shows Security CI running and turning green ✅
8) Daily Git workflow (the 90% you’ll use)
git status
git add .
git commit -m "clear message"
git push
What it does
status → see changes
add . → stage all changes
commit -m → snapshot with a message
push → trigger CI on GitHub
9) Troubleshooting quick fixes
Permission denied (publickey)
Check remote is SSH:
git remote -v
git remote set-url origin git@github.com:<USER>/<REPO>.git
Repository not found → create on GitHub first / check the spelling.
destination path exists → folder already exists; cd into it, don’t re-clone.
command not found: # → don’t paste comment lines.
heredoc> / dquote> → type EOF on a new line or press Ctrl+C.
CI red on ruff → run locally:
python3 -m pip install ruff
ruff check .
Fix, commit, push.
