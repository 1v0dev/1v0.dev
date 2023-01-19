---
title: "Fix Homebrew update freezing on MacOS"
date: 2023-01-19
#cover: /posts/images/
mermaid: false
tags:
- macos
- homebrew
keywords:
- homebrew
- TMP_FETCH_FAILURE
---

I recently had a problem with Homebrew on my Mac. When I tried to install something, for example `brew install docker` it will freeze on the command `Running brew update --preinstall`. 

Apparently there is a way to debug what happens by running `brew update -d` which will output the log of the update command. On my Mac it will block on `rm -f /opt/homebrew/.git/TMP_FETCH_FAILURE` (that is for M1 Mac, on Intel the path may be different). This seems to happen when running an old version of Homebrew. To fix it you need to run `brew update-reset`. That will fetch and reset the sources of Homebrew to the latest version, fixing the problem. Any subsequent runs of `brew update` will work just fine.