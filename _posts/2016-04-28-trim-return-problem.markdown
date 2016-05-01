---
layout: post
title:  "自动去除换行的小程序"
subtitle:   ""  
date:       2016-04-28
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
<!-- catalog: true -->
tags:
    - c#
    - GUI

---

因为下载PDF论文，文件的名字很多是一串编号，每次都需要手工打开文件复制名字，去掉换行，再改名字，比较的麻烦。主要是换行这步，特别烦，得拷贝到文本编辑器里，手工找换行。名字其实还好，比如有的时候做PPT，需要拷贝文字到PPT，那就好几行的换行了。于是我怒而写一个GUI程序，进行换行删除。

想着应该非常非常简单的，本着试试UWP的心态，第一个进行的是UWP的尝试。

# UWP
失败了。

```c#
private void RichEditBox_Paste(object sender, TextControlPasteEventArgs e)
{
    RichEditBox b = sender as RichEditBox;
    string txt;
    b.Document.GetText(Windows.UI.Text.TextGetOptions.None, out txt);
    Debug.WriteLine(txt);
    txt = txt.Replace("\n", string.Empty);
    txt = "hello world";
    b.Document.SetText(Windows.UI.Text.TextSetOptions.None, txt);
    if (txt != string.Empty)
    {
    }
}
```

![](/img/in-post/QQ截图20160429001339.png) 对文本进行赋值，居然是接着就值进行拼接的。。简直莫名其妙。之前我UWP/WPF失败是因为用了`TextBox`，这种控件只能输入单行，不能换行。所以带换行符的字符串会被截断。



# WPF
失败了。

# WinForm
成功实现功能。![](/img/in-post/QQ截图20160428235206.png)

``` c#
richTextBox1.Text = richTextBox1.Text.Replace("\n", "");
if(richTextBox1.Text != string.Empty)
{
    Clipboard.SetText(richTextBox1.Text);
}

```