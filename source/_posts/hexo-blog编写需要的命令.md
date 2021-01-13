---
title: hexo blog编写需要的命令
tags:
  - hexo
categories:
  - web 应用
description: 
date: 2021-01-08 08:40:47
---
  所有命令均在blog所在的根目录下执行。

1. 创建md文件

   ```shell
   hexo new post "hexo blog编写需要的命令"
   ```

2.  编写文件内容

 <!-- more -->

3. 生成js等网页渲染文件，上传到github

   ```shell
   hexo clean; hexo g; hexo d
   ```

4. 保存原始文件到github

   ```
   git add .; git commit; git push origin hexo
   ```

   



