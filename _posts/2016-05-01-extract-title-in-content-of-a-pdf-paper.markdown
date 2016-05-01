---
layout: post
title:  "提取PDF论文里的题目"
subtitle:   ""  
date:       2016-05-01
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
catalog: true
tags:
    - c#
    - python

---

估计很多人都会有这个需求，大量下载文献，然而下回来的文件名字并非文章标题，而是一些独立的标示字符串，每次手工修改，非常麻烦，于是打算写个工具直通提取标题，改名字。核心问题就是标题提取。当然如果标题有写到PDF文件的meta数据里，那就非常容易。但实际上大部分PDF文件都并没有规范的写入meta信息，所以这种方式不可行。

在网上找了下，大致上都会提到两个工具，一个是pyPDF,另一个是pdfminer。尝试了pypdf，它的是读取meta数据的，实际证明不可行。下面介绍使用pdfminer的方式。

# pdfminer

``` python
# -*- coding: utf-8 -*-

import sys
import re
from pdfminer.pdfparser import PDFParser
from pdfminer.pdfdocument import PDFDocument
from pdfminer.pdfinterp import PDFResourceManager, PDFPageInterpreter
from pdfminer.converter import PDFPageAggregator
from pdfminer.layout import LAParams, LTTextBox
from pdfminer.pdfpage import PDFPage


filename = u"D:\\lalalal\\xixixi\\10.1.1.258.8815.pdf"
fp = open(filename, 'rb')

parser = PDFParser(fp)
doc = PDFDocument(parser)
# doc.initialize()

rsrcmgr = PDFResourceManager()
laparams = LAParams()
device = PDFPageAggregator(rsrcmgr, laparams=laparams)
interp = PDFPageInterpreter(rsrcmgr, device)

pages = PDFPage.get_pages(fp)
first_page = pages.next()
interp.process_page(first_page)
layout = device.get_result()
textboxes = [i for i in layout if isinstance(i, LTTextBox)]
box_with_tallest_line = max(textboxes, key=lambda x: max(i.height for i in x))

text = box_with_tallest_line.get_text()
print re.sub('\s+', ' ', text).strip()
```

因为作者重构代码，2013年的时候pdfminer的API有较大的改变，于是我是跌跌撞撞，不断尝试才弄好的。但上面这个代码对部分PDF文件会解析出错，估计是字符集的问题。

```
Traceback (most recent call last):
  File "use_pyminer.py", line 29, in <module>
    interp.process_page(first_page)
  File "C:\Anaconda2\lib\site-packages\pdfminer\pdfinterp.py", line 832, in process_page
    self.render_contents(page.resources, page.contents, ctm=ctm)
  File "C:\Anaconda2\lib\site-packages\pdfminer\pdfinterp.py", line 843, in render_contents
    self.init_resources(resources)
  File "C:\Anaconda2\lib\site-packages\pdfminer\pdfinterp.py", line 347, in init_resources
    self.fontmap[fontid] = self.rsrcmgr.get_font(objid, spec)
  File "C:\Anaconda2\lib\site-packages\pdfminer\pdfinterp.py", line 186, in get_font
    font = PDFCIDFont(self, spec)
  File "C:\Anaconda2\lib\site-packages\pdfminer\pdffont.py", line 663, in __init__
    self.unicode_map = ttf.create_unicode_map()
  File "C:\Anaconda2\lib\site-packages\pdfminer\pdffont.py", line 434, in create_unicode_map
    assert 0
AssertionError
```

# apose pdf

这个提供C#的库。然而，好像没有类似pdfminer这种提供文本片段的扫描的API。[apose的文档](http://www.aspose.com/docs/display/pdfnet/Get+Page+Properties)提到`page`的一些属性。
![](/img/in-post/apose-pdf-image001.png)

```
    PageCollection pageCollection = pdfDocument.Pages;
    Page pdfPage = pageCollection[1];
    Console.WriteLine(pdfPage.Header.ToString());
```

我尝试了上面这样，然而其实那个页面并没有Header。其实这里header是跟footer对应的，相当于页头页尾（放页码那种）。然后我尝试了获取每页的`paragraph`,发觉每页`paragraph`数量也是0。

感觉经常想做的事情都没有什么简单明了的办法，最终搞半天还得放弃。

# 参考资料

http://stackoverflow.com/questions/5725278/how-do-i-use-pdfminer-as-a-library  
https://github.com/euske/pdfminer   
http://www.cnblogs.com/moqiguzhu/p/3764204.html   
http://www.aspose.com/docs/display/pdfnet/Get+Page+Properties  