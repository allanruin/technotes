---
layout: post
title:  "尝试使用xgboost"
subtitle:   ""  
date:       2016-06-14
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: false
tags:
    - xgboost
    - 大数据
---

# 安装

根据官方文档执行如下步骤，首先是从github上下载源码:      
`git clone --recursive https://github.com/dmlc/xgboost`  

然后执行下面命令进行编译安装  
`cd xgboost; make -j4`  

到pythyon包下安装python的接口  
`cd python-package; python setup.py install`  

这里因为我用的是anaconda的python，是在自己用户目录下，无需管理员权限，所以我去掉了`sudo`。

# 一些用法和参数

主要参数有这些：

```
# specify parameters via map, definition are same as c++ version
param = {'max_depth':2, 'eta':1, 'silent':1, 'objective':'binary:logistic' }
```

参数说明的文档在[这里](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md)
这里摘录一些说明。


另外当我需要用起这个工具，有两个“信息”需要弄明白。一是如何自定义损失函数。二是我需要准备什么样的输入格式。
第二个问题的回答是，输入格式是libsvm那种格式，就是第一个item是标签，之后都是index:value，间隔符是空格。    
第一个问题的，我找到了这个关于[自定义目标函数的示例文档](https://github.com/dmlc/xgboost/blob/master/demo/guide-python/custom_objective.py)。然后我有点糊涂了，几个概念：objective function,evaluation function。还有文档里注释说，

> note: what we are getting is margin value in prediction    
> NOTE: when you do customized loss function, the default prediction value is margin    
> this may make buildin evalution metric not function properly    
> for example, we are doing logistic loss, the prediction is score before logistic transformation    
> the buildin evaluation error assumes input is after logistic transformation    
> Take this in mind when you use the customization, and maybe you need write customized evaluation function

```
# user define objective function, given prediction, return gradient and second order gradient
# this is loglikelihood loss
def logregobj(preds, dtrain):
    labels = dtrain.get_label()
    preds = 1.0 / (1.0 + np.exp(-preds))
    grad = preds - labels
    hess = preds * (1.0-preds)
    return grad, hess
```

[这个SO的回答](http://stackoverflow.com/questions/34178287/difference-between-objective-and-feval-in-xgboost#)感觉可以解释目标函数和评价函数在xgboost中的区别。object函数用于训练过程，评价函数用于给模型打分（组合？）。

还有一个问题，我需要的是回归问题，文档里有两个回归选项。线性回归和逻辑斯蒂回归。

>"reg:linear" --linear regression    
>"reg:logistic" --logistic regression
