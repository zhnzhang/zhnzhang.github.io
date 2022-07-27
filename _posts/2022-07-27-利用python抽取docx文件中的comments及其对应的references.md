---
layout: post
title: 利用Python抽取DOCX文件中的Comments及其对应的References
author:
  name: Zhen Zhang
  link: https://github.com/zhnzhang
categories:
- Blogging
- Solutions
tags:
- Python
date: 2022-07-27 21:37 +0800
---
docx格式是基于XML的压缩文件格式，在MicroSoft Office 2007之后的版本中使用。下面我来讲讲如何利用Python抽取docx文件中的批注以及其所对应的原文部分。（注：后文我将批注称作comment，将批注所对应的原文部分称为reference）

目前网络上已经有博文讲到如何使用Python获取docx文档里的批注，比如：

- https://blog.csdn.net/qq_43479622/article/details/90580630

另外还有使用宏的方法来获取docx文档里批注的，但目前还没看到有博文讲到连同reference一起抽取的，python-docx的仓库里也有人提了[issue](https://github.com/python-openxml/python-docx/issues/483)。

使用到的Package：

- `python-docx`

- `lxml`

- `zipfile`

  ```python
  import zipfile
  from docx import Document
  from lxml import etree
  ```

定义好XML的namespace：

```python
ooXMLns = {'w': 'http://schemas.openxmlformats.org/wordprocessingml/2006/main'}
```

将docx文件解压缩后，word目录下的`comments.xml`和`document.xml`文件中记载了comments及其references的信息：

- 在`comments.xml`的w:comment标签里，我们可以找到comments相关的信息：

  - w:id属性里记录的是comment的id
  - w:t标签里记录的是comment的内容

- 在`document.xml`的w:commentRangeStart和w:commentRangeEnd标签之间，我们可以找到references相关的信息：

  - w:commentRangeStart和w:commentRangeEnd标签中都有w:id属性，对应comment的id
  - 上述两个标签之间的w:r标签中：
    - w:t标签里有w:id对应comment的reference的内容。

- 获取comment内容的代码：

  ```python
  def get_document_comments(docxFileName):
      """
      Function to extract all the comments of document(Same as accepted answer)
      Returns a dictionary with comment id as key and comment string as value
      """
      comments_dict = {}
      docxZip = zipfile.ZipFile(docxFileName)
      commentsXML = docxZip.read('word/comments.xml')
      et = etree.XML(commentsXML)
      comments = et.xpath('//w:comment', namespaces=ooXMLns)
      for c in comments:
          comment = c.xpath('string(.)', namespaces=ooXMLns)
          comment_id = c.xpath('@w:id', namespaces=ooXMLns)[0]
          comments_dict[comment_id] = comment
      return comments_dict
  ```

- 获取reference内容的代码：

  - 相比获取comment内容，需要先找到所有w:commentRangeStart节点，然后再移动到其后一个节点去取reference的content。

```python
def get_document_comments_references(docxFileName):
    """
    Function to extract all the references of comments of document
    Returns a dictionary with comment id as key and comment reference string as value
    """
    comments_references_dict = {}
    docxZip = zipfile.ZipFile(docxFileName)
    documentXML = docxZip.read('word/document.xml')
    et = etree.XML(documentXML)
    commentRangeStarts = et.xpath('//w:commentRangeStart', namespaces=ooXMLns)
    for commentRangeStart in commentRangeStarts:
        commentReferenceElem = commentRangeStart.getnext()
        commentReference = commentReferenceElem.xpath('string(.)', namespaces=ooXMLns)
        commentReferenceId = commentRangeStart.xpath('@w:id', namespaces=ooXMLns)[0]
        comments_references_dict[commentReferenceId] = commentReference
    return comments_references_dict
```



参考链接：

- https://stackoverflow.com/questions/47390928/extract-docx-comments
- https://stackoverflow.com/questions/27320773/lxml-xml-python-parser-go-to-next-element
