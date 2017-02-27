---
title: Mac上使用Python编程处理PDF
date: 2017-02-14 17:55:58
tags: 
---


## 系统环境 ##

- Mac 版本：macOS Sierra Version 10.12.2
- CPU: 3.1GHz Intel Core i7
- Memory: 16GB

## 安装软件 ##

- 升级pip

```
$ pip install --upgrade pip --user
```


- 安装python-pdfkit

```
$ pip install pdfkit
```

- 安装wkhtmltopdf

Mac版本通过Safari[进入页面下载](http://wkhtmltopdf.org/downloads.html)，或64-bit系统直接[点击下载](http://download.gna.org/wkhtmltopdf/0.12/0.12.4/wkhtmltox-0.12.4_osx-cocoa-x86-64.pkg)。

  注意，Mac下一定要通过Safari下载安装，否则会提示如下错误
  
```
com.apple.installer.pagecontroller error -1
```
参见：[Q: Cannot install .pkg and .mpkg files](https://discussions.apple.com/thread/2628710?start=0&tstart=0)。

要wkhtmltopdf是否安装成功，可以在命令行执行命令


```
wkhtmltopdf http://www.sina.com.cn google.pdf
```

- Python代码中执行HTML到PDF的转换

```
import pdfkit

pdfkit.from_url('http://google.com', 'out.pdf')
pdfkit.from_file('test.html', 'out.pdf')
pdfkit.from_string('Hello!', 'out.pdf')
```

## 参考资料

- [pdfkit 0.6.1](https://pypi.python.org/pypi/pdfkit/)：wkhtmltopdf工具的Wrapper，可以将HTML格式转成PDF格式
