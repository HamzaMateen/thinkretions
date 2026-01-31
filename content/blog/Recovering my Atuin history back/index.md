---
title: "Recovering my Atuin shell history back"
excerpt: "I lost my Atuin shell history, or it seemed so and I recovered it back."
date: 2026-01-31
tags:
  - nushell
  - atuin
  - shell
  - history 
  - recover
author: "Hamza Mateen"
draft: false
toc: true
layout: "blog"
readingTime: 1
slug: "recover-atuin-shell-history"
---

1. copy *~/.local/share/atuin*  to */somewhere/safe/path*
2. go to atuin official website, follow *uninstalling* steps
3. remove or disable *shell* integration
4. *reinstall*
5. enable shell integration. 
6. a new history setup is created at *~/.local/share/atuin*.
7. files starting with **history** important.
8. disable shell integration, and restart terminal.
9. delete those files starting with *history*
10. copy similar files already saved at */somewhere/safe/path* to *~/.local/share/atuin*
11. re-enable shell integration of atuin. (source *source ~/.local/share/atuin/init.nu*)

Enjoy!
