---
layout: single
title: "Fix Jupyter Notebook permission issue on Ubuntu 18.04"
author: "Danni"
tags:
  - Linux
---

>  "[Error 13] Permission denied"

I reinstalled Jupyter Notebook recently. However, there's an issue saying my saving file action was denied. I searched the internet and finally came up with a way to fix this common error.

```bash
Unexpected error while saving file: Untitled.ipynb [Errno 13] Permission denied: '/home/danni/.local/share/jupyter/notebook_secret'
```

First I checked GitHub and Stack Overflow posts. There are several threads stating the error is about. It seems that the Jupyter data files are **owned by root** and therefore your user may not write to them. This is most probably the case because you ran Jupyter as root. 

Run `sudo chown -R sura:sura /home/sura/.local/share/jupyter` and test if it works now. If it still does not work, check the error message. After the term "permission denied" is a path. Check the permissions of that path (`ls -l <path>`) and if the file or directory belongs to root, change then owner (`sudo chown -R sura:sura <path>`).

Afterwards, never run Jupyter as root, always as your user.

```bash
(venv) danni@danni-pc:~/Dropbox/CS/jupyter$ sudo chown -R danni:danni ~/.local/share/jupyter
```

### Installation

For setting up the Jupyter Notebook I referred to this [post](https://www.digitalocean.com/community/tutorials/how-to-set-up-jupyter-notebook-with-python-3-on-ubuntu-18-04) from Digital Ocean.
For fixing the issue I referred to this [post](https://askubuntu.com/questions/1038339/i-always-have-to-access-jupyter-notebook-as-a-root-user) on Stack Exchange.

