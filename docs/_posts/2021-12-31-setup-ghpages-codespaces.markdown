---
layout: post
title: Setup GitHub pages with Codespaces
permalink: /setup-gh-pages-codespaces
---

Here's a superquick way to setup GitHub pages for your account, if you haven't done this yet.
1. Just import a new repo from https://github.com/DanielMeixner/GitHubPagesSetup (don't fork it!). 
![Screenshot of import dialog](../images/20211231-import.jpg)
2. Name the repo YOUR-USERNAME.github.io (in my case DanielMeixner.github.io)
3. Open the new repo in a Codespace.

**That's it. You will find your new blog on https://YOUR-USERNAME.github.io in a few minutes. **

In the background automation will trigger a setup of your GitHub page using devcontainer automation and you can start blogging right away. 



> Hint:
> In case you already have another repo setup in YOUR-USERNAME.github.io you have to adjust the url in the _config.yml file to your new url. It will follow the pattern https://YOUR-USERNAME.github.com/YOUR-REPO-NAME . In this case you also have to explicitly enable Pages in the settings for your repo.