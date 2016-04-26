---
layout: post
title:  "获取所有驱动盘符"
subtitle:   ""  
date:       2016-4-24
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
<!-- catalog: true -->
tags:
    - cpp
    - windows

---

# 获取所有volume的GUID名称

可以利用`FindFirstVolume`,`FindNextVolume`,`FindVolumeClose`这几个API完成。


``` c++
	const size_t maxlen = 100;
	WCHAR volname[maxlen];
	WCHAR labelBuff[maxlen];
	auto h = FindFirstVolume(volname, maxlen);
	if (h != INVALID_HANDLE_VALUE) {
		getVolLabel(volname, labelBuff, maxlen);
		wprintf(L"%s\t%s\n", volname, labelBuff);
		while (FindNextVolume(h, volname, maxlen)) {
			getVolLabel(volname, labelBuff, maxlen);
			wprintf(L"%s\t%s\n", volname, labelBuff);
		}
		FindVolumeClose(h);
	}
```

这里的`getVolLabel`是我封装的获取volume的label的函数

```
void getVolLabel(PWCHAR volname, PWCHAR labelBuff, size_t labelBuffLen) {
	BOOL result = GetVolumeInformation(
		volname,
		labelBuff,
		labelBuffLen,
		nullptr,
		nullptr,
		nullptr,
		nullptr,
		0);
}
```

```
	WCHAR drivestringArr[maxlen] = { 0 };
	GetLogicalDriveStrings(maxlen, drivestringArr);
	wprintf(L"getlogical drive string:%.*s\n", maxlen, drivestringArr);

	for (int i = 0;i < maxlen;i++) {
		wprintf(L"%c ", drivestringArr[i]);
	}
	wprintf(L"\n");
```

效果如下