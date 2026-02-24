
# 🛠️ VS Code & Git Setup Notes

## ✅ Visual Studio Code Setup

### Recommended Extensions

Install the following extensions in VS Code:

- Terraform
- Kubernetes
- Docker
- YAML
- GitLens
- Python


## VS Code Settings

Enable the following:

- Format on Save
- Auto Save
- Set terminal default shell to zsh

This makes the VS Code terminal use your Mac’s default shell.


---

## 🔐 GitHub SSH Setup

### Generate SSH Key

ssh-keygen -t ed25519 -C "your_email@example.com"


### Copy Public Key

cat ~/.ssh/id_ed25519.pub


### Add SSH Key to GitHub

GitHub → Settings → SSH and GPG Keys → New SSH Key


Why SSH?

- No password required
- More secure
- Industry standard


---

## 📦 Clone Repository Using SSH

git clone git@github.com:username/repository.git
cd repository
code .


---

## ⚙️ Git Configuration

Set username and email:

git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"


---

## 🌿 Git Basic Workflow

Check status:

git status


Add changes:

git add .


Commit changes:

git commit -m "initial"


Push changes:

git push


---

## 🌱 Branch Workflow

View branches:

git branch


Switch to main:

git checkout main


Pull latest changes:

git pull origin main


Create feature branch:

git checkout -b feature/initial-setup


Verify branch:

git branch


---

## ✅ Recommended Daily Flow

git checkout main
git pull origin main
git checkout -b feature/<task-name>

# do work

git add .
git commit -m "message"
git push origin feature/<task-name>