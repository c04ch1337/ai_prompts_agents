# GitHub Development Guide for High Schoolers

## Table of Contents
1. [Understanding the Basics](#understanding-the-basics)
2. [Setting Up SSH for Both Laptops](#setting-up-ssh-for-both-laptops)
3. [Your Development Workflow](#your-development-workflow)
4. [Working on Multiple Features Across Two Laptops](#working-on-multiple-features-across-two-laptops)
5. [Cursor IDE Integration](#cursor-ide-integration)
6. [Lessons Learned & Best Practices](#lessons-learned--best-practices)
7. [Cheatsheet](#cheatsheet)

## Understanding the Basics

### What is GitHub?
Think of GitHub like Google Docs for code - it lets multiple people work on the same project without overwriting each other's work.

### Key Terms:
- **Repository (Repo)**: A project folder that contains all your files and their history
- **Commit**: A saved change (like saving a game level)
- **Branch**: A separate timeline for developing features
- **SSH**: A secure way to connect to GitHub without typing passwords

## Setting Up SSH for Both Laptops

### Step 1: Generate SSH Keys on Both Laptops

**On Ubuntu:**
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# Press Enter for default location
# Add a secure passphrase
```

**On Windows:**
```bash
# Open Command Prompt or PowerShell
ssh-keygen -t ed25519 -C "your_email@example.com"
# Press Enter for default location
# Add a secure passphrase
```

### Step 2: Add SSH Keys to SSH Agent

**On Ubuntu:**
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

**On Windows:**
```bash
# Start SSH agent
ssh-agent -s
ssh-add ~/.ssh/id_ed25519
```

### Step 3: Add SSH Keys to GitHub

1. Copy your public key:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   # Select and copy the entire output
   ```

2. Go to GitHub.com → Settings → SSH and GPG keys → New SSH key
3. Paste the key for each laptop (name them "Ubuntu Laptop" and "Windows Laptop")
4. Repeat for both laptops

### Step 4: Test SSH Connection
```bash
ssh -T git@github.com
# You should see: "Hi c04ch1337! You've successfully authenticated..."
```

## Your Development Workflow

### Phase 1: Initial Setup (One-Time)
```bash
# Clone your repository (do this on both laptops)
git clone git@github.com:c04ch1337/your-repo-name.git
cd your-repo-name
```

### Phase 2: Daily Development Cycle

#### 1. Start Fresh Each Day
```bash
git pull origin main
```

#### 2. Create a Feature Branch
```bash
git checkout -b feature/your-feature-name
```

#### 3. Develop Your Feature
- Write code in Cursor
- Test your changes
- Commit regularly

#### 4. Save Your Work
```bash
git add .
git commit -m "Describe what you did"
```

#### 5. Share Your Work
```bash
git push origin feature/your-feature-name
```

## Working on Multiple Features Across Two Laptops

### Strategy: Feature Branching

**Scenario**: You want to work on Feature A on Ubuntu and Feature B on Windows

#### On Ubuntu Laptop (Feature A):
```bash
# Start fresh
git pull origin main

# Create feature branch
git checkout -b feature/login-system

# Work on your feature...
# Make changes in Cursor
git add .
git commit -m "Add login form UI"

# Push to GitHub
git push origin feature/login-system
```

#### On Windows Laptop (Feature B):
```bash
# Start fresh
git pull origin main

# Create different feature branch
git checkout -b feature/user-profile

# Work on your feature...
# Make changes in Cursor
git add .
git commit -m "Create user profile page"

# Push to GitHub
git push origin feature/user-profile
```

### Switching Between Laptops Mid-Feature

If you start a feature on Ubuntu and want to continue on Windows:

**On Ubuntu (before switching):**
```bash
git push origin feature/your-feature
```

**On Windows (when continuing):**
```bash
git fetch origin
git checkout -b feature/your-feature origin/feature/your-feature
```

## Cursor IDE Integration

### Setup Cursor for Git:
1. Open your project in Cursor
2. Install the GitHub extension in Cursor
3. Enable Git integration in settings

### Useful Cursor Git Commands:
- `Ctrl+Shift+P` → "Git: Clone" to clone repositories
- Use the Source Control tab (Ctrl+Shift+G) to see changes
- Click the "+" to stage files
- Type commit message and press Ctrl+Enter to commit

### Cursor Rules for Git:
1. **Commit Often**: Small, frequent commits are better than huge ones
2. **Descriptive Messages**: Write clear commit messages
3. **Test Before Committing**: Don't commit broken code

## Lessons Learned & Best Practices

### ✅ Do's:
- **Pull before you start**: Always `git pull` before beginning work
- **One feature per branch**: Keep features separate
- **Commit often**: Small commits are easier to understand
- **Write good commit messages**: Use the format: "verb + what changed"
- **Use descriptive branch names**: `feature/login` not `new-stuff`

### ❌ Don'ts:
- **Don't work on main**: Always create feature branches
- **Don't commit half-working code**: Make sure it compiles/runs
- **Don't forget to push**: Save your work to GitHub regularly
- **Don't mix features**: One branch = one feature

### Common Issues & Solutions:

**Problem**: "Your local changes would be overwritten"
```bash
git stash
git pull
git stash pop
```

**Problem**: Wrong branch
```bash
git checkout correct-branch-name
```

**Problem**: Merge conflicts
- Open the conflicted files in Cursor
- Look for `<<<<<<<`, `=======`, `>>>>>>>` markers
- Choose which changes to keep
- Remove the markers
- `git add .` and `git commit`

## Cheatsheet

### Daily Commands:
```bash
# Start working
git pull origin main
git checkout -b feature/name

# During work
git add .
git commit -m "description"
git push origin feature/name

# Finish work
git checkout main
git pull origin main
```

### Branch Management:
```bash
# See all branches
git branch -a

# Switch branches
git checkout branch-name

# Delete local branch
git branch -d branch-name

# Delete remote branch
git push origin --delete branch-name
```

### Status & Info:
```bash
# See what changed
git status

# See commit history
git log --oneline

# See remote repositories
git remote -v
```

### SSH Troubleshooting:
```bash
# Test SSH connection
ssh -T git@github.com

# Check SSH keys
ls -al ~/.ssh

# Restart SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### Workflow Summary:
1. **Ubuntu Day**: `git pull` → `git checkout -b feature/A` → code → commit → push
2. **Windows Day**: `git pull` → `git checkout -b feature/B` → code → commit → push
3. **Switch Mid-Feature**: Push from current laptop → pull on other laptop

Remember: GitHub is your friend! It keeps your code safe and lets you work seamlessly between laptops. Happy coding! 🚀
