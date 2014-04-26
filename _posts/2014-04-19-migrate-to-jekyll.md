---
layout: post
title: "迁移到jekyll"
description: "从wordpress迁移到jekyll"
category: tech
tags: [jekyll, ruby]
---
{% include JB/setup %}

刚刚迁移过来，百废待兴。。。
第一篇就说说如何迁移到jekyll吧~

## Overview

### 注意点:
- jekyll serve的--detach参数与--watch参数不能共用，detach会导致autorebuild功能失效。这是一个已知问题。(具体可参见#1742)
- 使用自动build功能时，指定配置文件不然每次修改其它文件都会导致配置文件中的修改内容被覆盖。
