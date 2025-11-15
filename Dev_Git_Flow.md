# 1. Start work session
git fetch origin
git merge origin/main

# 2. Do your work
# 3. Commit changes
git add .
git commit -m "Describe your changes"

# 4. Sync again before pushing
git fetch origin  
git merge origin/main

# 5. Push to remote
git push origin branch1
