---
layout: post
title:  "git如何清空历史提交记录"
subtitle:   ""  
date:       2016-04-23
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
<!-- catalog: true -->
tags:
    - git
    - 操作技巧

---

记录如何清掉之前的提交历史。比如你clone了个博客模板，即使删掉了`.git`目录，再重新提交也带有git提交信息。那么可以自行下面的操作

```
git checkout --orphan latest_branch // Checkout
git add -A                          // Add all the files
git commit -am "commit message"     //Commit the changes
git branch -D master                //Delete the branch
git branch -m master                //Rename the current branch to master
git push -f origin master           //Finally, force update your repository
```

关于checkout中使用的`--orphan`选项： 
Create a new orphan branch, named <new_branch>, started from <start_point> and switch to it. The first commit made on this new branch will have no parents and it will be the root of a new history totally disconnected from all the other branches and commits.