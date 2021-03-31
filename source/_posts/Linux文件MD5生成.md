---
title: 文件MD5批量生成
copyright: true
date: 2018-10-18 16:46:14
tags: MD5
categories: Linux
keywords:
- Linux
description:
---

```bash
find ./ -type f | xargs md5sum > a.md5
```

