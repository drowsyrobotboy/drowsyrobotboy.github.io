---
title: How to fix ‘Downloading VS Code Server failed’ error
date: 2021-03-21T12:00:00+05:30
draft: false
tags:
  - troubleshooting
categories:
  - troubleshooting
---
While using VS Code’s [Remote Development extension pack](https://aka.ms/vscode-remote/download/extension?ref=drowsyrobotboy.com), you may get the following error

```
remote-ssh@0.44.2
win32 x64
...
...
> 
> Running remote connection script
> 
> Installing to /home/maruthi/.vscode-server/bin/2213894ea0415ee8c85c5eea0d0ff81ec
> c191529...
> Downloading with wget
> 
> Installing to /home/maruthi/.vscode-server/bin/2213894ea0415ee8c85c5eea0d0ff81ecc191529...
> Downloading with wget
> 
> 
...
...

> ERROR: cannot verify update.code.visualstudio.com's certificate, issued by ‘emailAddress=certadmin@netskope.com,CN=XXX’: Self-signed certificate encountered. To connect to update.code.visualstudio.com insecurely, use `--no-check-certificate'.
> 41d06654-7768-475d-9db9-87cf9bc644b0##25##
> 
"install" terminal command done
Received install output: 41d06654-7768-475d-9db9-87cf9bc644b0##25##
Server download failed
Downloading VS Code Server failed. Please try again later.
```

To resolve this issue,

Add a line of :

```
check-certificate=off
```

to your `.wgetrc` file under the user’s home directory (on the target system)

_**Note**: It will disable the SSL certificate check for all wget commands you use, unless you change it to : `check-certificate=on`_