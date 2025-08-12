
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



