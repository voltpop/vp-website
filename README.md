# VoltPop website
This website utilizes GitHub and Netlify to create a free staging / production environment
leveraging gh_pages as a staging branch and master(or main) as the production branch.

# Making Changes
Changes can be made through the GitHub website! no need to be a tech junkie here!
1. Log in with a GitHub account
1. Find the file you want to edit
1. Open editing for the file by clicking the pencil in the top left corner
1. Start making your changes using markdown!
1. Commit your changes with a helpful note

# Deploying changes
1. Verify your changes in [staging](https://warwalrux.github.io/voltpopulous_site/)
2. Select Pull Requests
3. Open a New Pull Request with the green button on the left
4. Change the `base:` dropdown to `master`
  * Changes should now appear below

testing new process with github protection on master

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
