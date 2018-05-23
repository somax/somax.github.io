---
title: "将 Teambition 分享迁移到 hugo"
date: 2018-05-23T23:06:25+08:00
authors: ["somax"]
tags: 
  - hugo
---

[Teambition](https://www.teambition.com) 是一个非常好的团队协作工具，公司内部一直使用它做项目管理和知识分享的平台。

经过几年的积累，在公司 TB 知识管理项目中已经发布了三百来条分享记录（自己都没想到有这么多了），其中有很多是可以公开的，所以利用之前研究 TB API 的项目的基础上，完成了批量导出并自动生成 hugo 格式文档的工具 [ somax/teambition-sync](https://github.com/somax/teambition-sync) 。

虽然 hugo 生成 300 篇文章只需要 `2250ms`，不过还是需要人工筛选一下，后续会慢慢（也许很快）上传。