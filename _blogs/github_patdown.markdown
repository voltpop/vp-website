---
layout: single
permalink: /blogs/github_pat/
collection: blogs
title: Configuring GitHub PAT
date: 2021-08-27
excerpt_separator: <!--excerpt-->

---
## It's PAT!
<!--excerpt-->

GitHub has officially phased out their username / password method for checking in changes. Since August 13th, they've begun to require either SSH based checkouts (which are _pretty nice_) or PAT (Personal Access Token) based authentication.

I really like the way that GitHub implements SSH based checkouts, however, personal access tokens are great if you're using more than one GitHub account (which I am) and while it's totally [possible](https://stackoverflow.com/questions/3860112/multiple-github-accounts-on-the-same-computer) to have multiple ssh keys for multiple accounts, it seems a little bit like overkill to me... and doubly so since I use more than one machine for development.

### Setting up a Personal Access Token

The [Official Github Directions](https://docs.github.com/en/enterprise-server@2.22/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) provide a very nice step by step instruction on how to create a personal access token, so I'll gloss over it by saying that they can be created, named, and scoped under `Settings > Developer settings > Personal access tokens`.

According to the the GitHub Documentation, PATs can be used much in the same way as passwords. I _highly_ recommend dropping this into your favorite password manager (*cough* bitwarden *cough*).


### Making the most of your PATs with GitHub Credential Helper

There are a ton of ways to store your PAT so that you can use it whenever the mood strikes you. In this particular instance however, I'm going to _highly_ recommend using a git-credentials helper. That sounds very fancy, and may well indeed _be_ fancy, but it's still just putting your credentials into a text file somewhere on the filesystem so big deal right? In order to leverage the automagical wonder that is git-credentials all you need to do is create a credentials file and set the config!

```
$ git config credential.helper store
$ echo "https://username:PersonalAccessToken@github.com" >> ~/.git-credentials
$ git push # to test your changes```

#### PAT as an envvar

IF you don' share your machine with any other non-muggles (read: non-technical people), then storing your PATs as environment variables isn't a bad way to go. Also there is a long and storied history of people creating overly complicated
.bashrc and .profile files. I, like many fine linux folks have an overly complicated system of customizations built up over the years myself, and just one of the things I've done for myself is to define my GitHub account and token in the appropriate format and export it as an environment variable.

`export $username="username:ghp_longTokenStringHere"` 

This allows a user to run the clone operation with minimal workflow changes:

`git clone $username@github.com:/org/repo`

### Updating your pre-existing checkouts

This is probably the most difficult part of the exercise: updating your checkouts (after you remember where you checked them out...). Fortunately, the actual updating part is pretty painless as it's just an origin url update. Provided you've used envvar method from above this should be as easy as running the following:

`git remote set-url origin $username@github.com/org/repo`

If you've opted to use the git credential.helper that's cool too, it may suit your purposes to start a new bash shell, temporarily export your GitHub User:Token, update your repos, then kill your shell. So the token doesn't persist as an environment variable.

### In Conclusion: GitHub is awesome

Just thought is bore repeating. From SSH keys to PATs it seems GitHub just keeps making it easier and easier to save your code, safely.

