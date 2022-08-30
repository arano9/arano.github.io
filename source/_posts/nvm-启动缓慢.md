---
layout: post
title: nvm 启动缓慢
date: 2021-11-09 22:23:22
tags: shell nvm
catagories: note
---

nvm管理node版本后启动很慢

因此写了个alias先用用，还算有点用（放置在.bash_profile）

```shell
alias nvm='unalias nvm  && . "/usr/local/opt/nvm/nvm.sh" && nvm '
```

