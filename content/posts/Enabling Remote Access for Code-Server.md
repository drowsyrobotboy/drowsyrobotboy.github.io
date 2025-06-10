---
title: Enabling Remote Access for Code-Server
date: 2021-03-09T12:00:00+05:30
draft: false
tags:
  - vscode
  - server
categories:
  - troubleshooting
---
Open port 8080 on server/machine that is running code-server

Then, in code-server’s `config.yaml`, update bind address to `0.0.0.0`