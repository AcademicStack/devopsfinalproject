# Version Control Quick Reference

## Repository Setup

```bash
mkdir lab2
cd lab2
git init
touch README.md
git add .
git commit -m "Initial Commit"
```

## Linking to GitHub

```bash
git remote add origin https://github.com/username/repo-name.git
git pull origin main
git push origin main
```

## Cloning a Repository

```bash
git clone https://github.com/username/repo-name.git
git clone git@github.com:username/repo-name.git
```

## SSH Key Setup

```bash
ssh-keygen -t rsa -b 4096 -C your_email@example.com
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub
ssh -T git@github.com
```

## Daily Workflow

```bash
git add .
git add README.md
git commit -m "your message here"
git push origin main
git pull
```

## Git Identity Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "you@email.com"
```

## Branches

```bash
git branch feature-branch          # create branch
git checkout feature-branch        # switch to branch
git switch feature-branch          # modern alternative
git checkout master                # return to master
git merge feature-branch           # merge into current branch
git push origin feature-branch     # push branch to GitHub
```

## Resolving a Merge Conflict

1. Run `git merge feature-branch` — Git flags the conflict
2. Open the file — conflict markers look like this:

```
<<<<<<< HEAD
Changes from master branch
=======
Changes from feature-branch
>>>>>>> feature-branch
```

3. Edit the file to keep the correct content
4. Stage and complete the merge:

```bash
git add README.md
git commit -m "Resolved merge conflict between main and feature-branch"
```

## GitHub Actions — deploy.yml

```yaml
name: Deploy to EC2

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    - name: Deploy to EC2
      env:
        PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
        HOST: ${{ secrets.EC2_HOST }}
        USER: ec2-user
      run: |
        echo "$PRIVATE_KEY" > private_key.pem
        chmod 600 private_key.pem
        ssh -o StrictHostKeyChecking=no -i private_key.pem ${USER}@${HOST} '
        cd /path/to/your/repo &&
        git pull origin main &&
        docker compose down &&
        docker compose up -d
        '
```

## GitHub Secrets to Configure

| Secret Name | Value |
|---|---|
| `EC2_SSH_KEY` | Contents of your `.pem` private key file |
| `EC2_HOST` | EC2 Public IPv4 DNS |

*Settings → Secrets and variables → Actions → New repository secret*

## Markdown Index File (index.md)

```markdown
# Project Documentation

- [Version Control](version-control.md)
- [Linux Fundamentals](linux-fundamentals.md)
- [Cloud Infrastructure](cloud-infrastructure.md)
- [Containerization](containerization.md)
```

