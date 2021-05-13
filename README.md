# VoltPop website
This website utilizes GitHub and Netlify to create a free staging / production environment
leveraging gh_pages as a staging branch and master(or main) as the production branch.

# Making Changes
Changes can be made through the GitHub website! no need to be a tech junkie here!
1. Log in with a GitHub account
2. CHANGE THE BRANCH TO `gh_pages` THIS IS THE STAGING BRANCH
3. Find the file you want to edit
4. Open editing for the file by clicking the pencil in the top left corner
5. Start making your changes using markdown!
6. Commit your changes with a helpful note

# Deploying changes
1. Verify your changes in [staging](https://warwalrux.github.io/voltpopulous_site/)
2. Once committed you should be returned to the front page (the one that shows this doc!)
3. Just below the branch dropdown and green `code` button, you'll see that the branch
   is ahead of `master`, from here select `pull request`.
5. Once you're on the PR page, it should say that code can be merged safely.
6. merge it!
7. check out the changes on [production](https://voltpop.com)

# Technical Bits
## Site creation
1. Create the GitHub repository
1. Jekyll magic:
  1. `git clone https://github.com/warwalrux/new_site`
  1. `cd new_site`
  1. `jekyll new .`
  1. configure `remote_theme` for GitHub
  1. configure `Gemfile` for GitHub
  1. commit the things
1. Hook domain up to netlify (surprisingly simple)
1. Configure domain to serve repo contents
