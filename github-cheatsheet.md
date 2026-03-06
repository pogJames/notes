## push existing repo to another one as remote
```bash
# Check your current remotes (probably only "origin" = your personal GitHub)
git remote -v

# Add the company repo as a second remote (name it "company" or whatever you like)
git remote add <company> https://github.com/company-org/your-project-name.git
# or with SSH: git remote add <company> git@github.com:company-org/your-project-name.git

# Push the main branch (or master, depending on your default)
git push <company> main

# If you want to push all branches and tags too (recommended)
git push <company> --all
git push <company> --tags
```
