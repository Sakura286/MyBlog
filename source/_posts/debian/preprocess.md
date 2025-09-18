---
title: Debian 打包脚本预处理
date: 2025-09-17 15:02:35
tags:
- linux
- debian
- repology
---

有时候我们想要更灵活地处理 Debian 的打包脚本，使得我们可以通过参数来影响 control/install 之类的文件，那么应该怎么办呢？

# 1. install 的处理

可以在 install 文件里留一个占位符，然后在 rules 文件里用`sed`进行替换

*.install:

```makefile
opt/@FOO_VERSION@/bin
```

rules:

```makefile
foo_version="6.4.3"


override_dh_install:
        sed -i "s,@FOO_VERSION@,${foo_version},g" debian/*.install
        dh_install
```

将文件从 debian/tmp 安装到 debian/${pkg_name}，是发生在 dh_install 阶段的。所以只要在 dh_install 之前完成替换即可。

# 2. control 的处理

**TODO**