### 🚀 Basic Loop (Commit and Send)



```Bash
git status # See what has changed
git add .                      # Add ALL changed files to the index
git add path/to/file # Add only a specific file
git commit -m "Message" # Commit changes with comment
git push origin main # Push commits to GitHub to the main branch
```

### 🔄 Get updates from GitHub



```Bash
git pull origin main # Download and immediately merge the latest changes from the site
git fetch # Just check/download changes without merging
```

### 🛠️ Fixing jambs (Cancel and clear)



```Bash
git restore . # Reset all saved local changes (dangerous!)
git restore path/to/file # Reset changes to only one file
git checkout -b new-branch # Create a new branch and immediately jump into it
git branch -D branch-name # Delete Vecti locally
```

### 🏷️ Tags and Releases (For assembly in Actions)



```Bash
git tag -a v1.0.0 -m "Release v1.0.0" # Create a local version tag
git push origin v1.0.0 # Push the tag to GitHub (will trigger the release)
git tag -d v1.0.0 # Remove tag locally
git push origin --delete v1.0.0 # Delete a tag on GitHub
```

### 📜 History and comparison



```Bash
git log --oneline # Brief history of commits (one line per commit)
git diff # View uncommitted edits directly in the terminal
```
