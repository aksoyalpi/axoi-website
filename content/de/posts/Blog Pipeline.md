---
title: Blog Pipeline
date: 2025-04-20
draft: false
tags:
  - axoi
  - blog
---

# Obisidian

# PowerShell
```shell
robocopy "C:\Users\aksoy\OneDrive\Dokumente\Obsidian\my_second_brain\posts" "C:\Users\aksoy\dev\Website\axoi\content\posts" /mir

hugo server -t hugo-coder
```

!![Image Description](/images/Pasted%20image%2020250420201259.png)

```python
import os

import re

import shutil

  

# Paths (using raw strings to handle Windows backslashes correctly)

posts_dir = r"C:\Users\aksoy\OneDrive\Dokumente\Obsidian\my_second_brain\posts"

attachments_dir = r"C:\Users\aksoy\OneDrive\Dokumente\Obsidian\my_second_brain\Attachments"

static_images_dir = r"C:\Users\aksoy\dev\Website\axoi\static\images"

  

# Step 1: Process each markdown file in the posts directory

for filename in os.listdir(posts_dir):

    if filename.endswith(".md"):

        filepath = os.path.join(posts_dir, filename)

        with open(filepath, "r", encoding="utf-8") as file:

            content = file.read()

        # Step 2: Find all image links in the format ![Image Description](/images/Pasted%20image%20...%20.png)

        images = re.findall(r'\[\[([^]]*\.png)\]\]', content)

        # Step 3: Replace image links and ensure URLs are correctly formatted

        for image in images:

            # Prepare the Markdown-compatible link with %20 replacing spaces

            markdown_image = f"![Image Description](/images/{image.replace(' ', '%20')})"

            content = content.replace(f"[[{image}]]", markdown_image)

            # Step 4: Copy the image to the Hugo static/images directory if it exists

            image_source = os.path.join(attachments_dir, image)

            if os.path.exists(image_source):

                shutil.copy(image_source, static_images_dir)

  

        # Step 5: Write the updated content back to the markdown file

        with open(filepath, "w", encoding="utf-8") as file:

            file.write(content)

  

print("Markdown files processed and images copied successfully.")
```

# Push to Github
```shell
# Step 8: Push the public folder to the hostinger branch using subtree split and force push
echo "Deploying to GitHub Hostinger..."
git subtree split --prefix public -b hostinger-deploy
git push origin hostinger-deploy:hostinger --force
git branch -D hostinger-deploy

```

# The Mega Script
```shell
# PowerShell Script for Windows

# Set variables for Obsidian to Hugo copy
$sourcePath = "C:\Users\aksoy\OneDrive\Dokumente\Obsidian\my_second_brain\posts"
$destinationPath = "C:\Users\aksoy\dev\Website\axoi\content\posts"

# Set Github repo 
$myrepo = git@github.com:aksoyalpi/axoi-website.git

# Set error handling
$ErrorActionPreference = "Stop"
Set-StrictMode -Version Latest

# Change to the script's directory
$ScriptDir = Split-Path -Parent $MyInvocation.MyCommand.Definition
Set-Location $ScriptDir

# Check for required commands
$requiredCommands = @('git', 'hugo')

# Check for Python command (python or python3)
if (Get-Command 'python' -ErrorAction SilentlyContinue) {
    $pythonCommand = 'python'
} elseif (Get-Command 'python3' -ErrorAction SilentlyContinue) {
    $pythonCommand = 'python3'
} else {
    Write-Error "Python is not installed or not in PATH."
    exit 1
}

foreach ($cmd in $requiredCommands) {
    if (-not (Get-Command $cmd -ErrorAction SilentlyContinue)) {
        Write-Error "$cmd is not installed or not in PATH."
        exit 1
    }
}

# Step 1: Check if Git is initialized, and initialize if necessary
if (-not (Test-Path ".git")) {
    Write-Host "Initializing Git repository..."
    git init
    git remote add origin $myrepo
} else {
    Write-Host "Git repository already initialized."
    $remotes = git remote
    if (-not ($remotes -contains 'origin')) {
        Write-Host "Adding remote origin..."
        git remote add origin $myrepo
    }
}

# Step 2: Sync posts from Obsidian to Hugo content folder using Robocopy
Write-Host "Syncing posts from Obsidian..."

if (-not (Test-Path $sourcePath)) {
    Write-Error "Source path does not exist: $sourcePath"
    exit 1
}

if (-not (Test-Path $destinationPath)) {
    Write-Error "Destination path does not exist: $destinationPath"
    exit 1
}

# Use Robocopy to mirror the directories
$robocopyOptions = @('/MIR', '/Z', '/W:5', '/R:3')
$robocopyResult = robocopy $sourcePath $destinationPath @robocopyOptions

if ($LASTEXITCODE -ge 8) {
    Write-Error "Robocopy failed with exit code $LASTEXITCODE"
    exit 1
}

# Step 3: Process Markdown files with Python script to handle image links
Write-Host "Processing image links in Markdown files..."
if (-not (Test-Path "images.py")) {
    Write-Error "Python script images.py not found."
    exit 1
}

# Execute the Python script
try {
    & $pythonCommand images.py
} catch {
    Write-Error "Failed to process image links."
    exit 1
}

# Step 4: Build the Hugo site
Write-Host "Building the Hugo site..."
try {
    hugo
} catch {
    Write-Error "Hugo build failed."
    exit 1
}

# Step 5: Add changes to Git
Write-Host "Staging changes for Git..."
$hasChanges = (git status --porcelain) -ne ""
if (-not $hasChanges) {
    Write-Host "No changes to stage."
} else {
    git add .
}

# Step 6: Commit changes with a dynamic message
$commitMessage = "New Blog Post on $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')"
$hasStagedChanges = (git diff --cached --name-only) -ne ""
if (-not $hasStagedChanges) {
    Write-Host "No changes to commit."
} else {
    Write-Host "Committing changes..."
    git commit -m "$commitMessage"
}

# Step 7: Push all changes to the main branch
Write-Host "Deploying to GitHub Master..."
try {
    git push origin master
} catch {
    Write-Error "Failed to push to Master branch."
    exit 1
}

# Step 8: Push the public folder to the hostinger branch using subtree split and force push
Write-Host "Deploying to GitHub Hostinger..."

# Check if the temporary branch exists and delete it
$branchExists = git branch --list "hostinger-deploy"
if ($branchExists) {
    git branch -D hostinger-deploy
}

# Perform subtree split
try {
    git subtree split --prefix public -b hostinger-deploy
} catch {
    Write-Error "Subtree split failed."
    exit 1
}

# Push to hostinger branch with force
try {
    git push origin hostinger-deploy:hostinger --force
} catch {
    Write-Error "Failed to push to hostinger branch."
    git branch -D hostinger-deploy
    exit 1
}

# Delete the temporary branch
git branch -D hostinger-deploy

Write-Host "All done! Site synced, processed, committed, built, and deployed."

```