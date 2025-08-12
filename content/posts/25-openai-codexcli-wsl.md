---
title: "How to run OpenAI Codex CLI under Windows using WSL"
date: 2025-08-12T14:14:09+03:00
tags:
    - AI
keywords:
    - OpenAI
    - CodexCLI
    - WSL
---

OpenAI [Codex CLI](https://github.com/openai/codex) is generally build to run under Linux. While running natively under
Windows is possible, my experience is that is buggy and not always work as expected. So if you have Windows machine,
using WSL is a lot better, but it does require some configuration.

<!--more-->

# Why using Codex CLI under Windows is bad?

Using the latest version (0.20.0 at the time of writing) under Windows leaves a lot to be desired. First I tried version
0.19.0, but it had massive bug which did not allow you to login using ChatGPT account. 0.20.0 fixed that and a bunch 
of other windows stuff, but still, using `--full-auto` mode did not work. Codex CLI expected that linux commands like `ls`
and `cat` are available, and because they weren't it failed to correctly find and parse the project files. It fell back 
to using powershell alternatives, but they often fail and even using auto mode without approval policy, Codex CLI asked
to approve each command. It's OK for simple edits, but if you asked it to parse a bunch of files and create several
new ones it becomes annoying pretty fast. I had to press "Approve" on each action which means I have to sit there
and stare at the terminal.

All this will hopefully improve in the future but current situation is not good.

# Running under WSL

Using the Windows Subsystem for Linux worked great, but it did require some subtle configurations which I will detail 
below.

## Install and start WSL

There are a lot of great guides on WSL, so I don't want to repeat the instruction here. This is a very detailed one
which I recommend - [Windows Subsystem for Linux 2 The Complete Tutorial](https://www.sitepoint.com/wsl2/)

I am using Ubuntu but the instructions should work with any distribution.
To install it: `wsl --install -d Ubuntu` and then run it `wsl -d Ubuntu`

## Disable appendWindowsPath

By default, WSL will append Windows PATH elements to $PATH in linux. Which means if you already have NodeJS or Codex
installed in Windows, and part of your PATH, you can invoke those commands under Linux. But we want to have those
installed under Ubuntu so they can work correctly. Therefore, we need to disable that functionality.

In Ubuntu edit the wsl.conf:
`sudo nano /etc/wsl.conf`

and add this section at the end:
```
[interop]
enabled=true
appendWindowsPath=false
```

Save the file and restart Ubuntu (exit, `wsl --shutdown` and enter Ubuntu again).

You can read more about the advanced options of WSL [here](https://learn.microsoft.com/en-us/windows/wsl/wsl-config)

## Install NodeJS

Codex CLI needs node and npm so we need to install them in WSL. The versions in Ubuntu repos are old, and Codex
needs newer ones so we can't use `apt`. Instead, we need NVM.
Microsoft has pretty detailed guide - [Install Node.js on Windows Subsystem for Linux (WSL2)](https://learn.microsoft.com/en-us/windows/dev-environment/javascript/nodejs-on-wsl)

But if you don't want to read it:
1. install nvm: `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash`
2. install node `nvm install node`
3. check if npm works `npm --version`

## Install WSLU so we can authenticate using ChatGPT account

If you are using API Key for Codex CLI you can skip this section.
In order to authenticate with ChatGPT, Codex CLI opens a browser. Since we are under WSL we don't have one and
the authentication hangs. We can open Windows apps from inside WSL but we need a little help from the WSLU package.

1. install WSLU: `sudo apt install -y wslu`
2. set BROWSER env variable: `echo 'export BROWSER=wslview' >> ~/.bashrc`
3. source bash so bashrc is executed: `source ~/.bashrc`

Now we are all set, you can try running `wslview https://example.com` and it should open in the default Windows browser.

## Install Codex CLI

Finally, we can install Codex CLI by running a simple command:
`npm install -g @openai/codex`  
and then run it `codex`

You should be able to correctly authenticate using ChatGPT or API Key.

## Use Codex CLI

So now everything should be running. To use Codex CLI open a Windows terminal in the root folder of you project.
Then start WSL, it will start in the current folder by default: `wsl`.
Finally, in the WSL shell start codex `codex`.



