---
layout: post
author: fri3nds
title: 如何使用 pandoc 和 latex 制作漂亮的 pdf
category: 技术博文
tag: [pandoc latex]
description: 'markdown 是很多程序员都喜爱的文档格式，简洁切功能强大，通过一些 markdown 文本编辑器可以方便导出成 pdf 文件，但是不同的 markdown 支持的主题导出的 pdf 差别还是很大，很多现代编译器在编辑过程中，并不能很好地发现 ‘零宽度字符’, 导致出现了一些很难发现的 bug。'
---

## 1. 前言

markdown 是很多程序员都喜爱的文档格式，简洁切功能强大，通过一些 markdown 文本编辑器可以方便导出成 pdf 文件，但是不同的 markdown 支持的主题导出的 pdf 差别还是很大，很多现代编译器在编辑过程中，并不能很好地发现 ‘零宽度字符’, 导致出现了一些很难发现的 bug。即使是这样 markdown 还是我的最爱，快速且方便，随便文本编辑器都可以打开，也可以看懂，只是没有 preview 的功能。

但是有一些需要输出到其他格式文件时，markdown 就有点显得不太够用。这个时候就需要我们今天的主角之一的 `pandoc`

## 2. 最强文本转换工具 Pandoc

[pandoc](https://pandoc.org/) 是用 haskell 编写的一个 cli 工具，可以支持 docx、epub、html、json、latex、markdow、vimwiki、org、asciidoc 等等格式之间互相转换。mac、windows、linux 的安装也都是很简单，官方都有安装教程。

## 3. 最强文本编排工具 LaTeX/TeX

LaTeX，是一种基于 TeX 的排版系统。 TeX 是由美国计算机 Donald Ervin Knuth 编写的排版软件。至于 Donald Ervin Knuth 真的是神人。Donald Ervin Knuth 编写 TeX 的原因，就是当时计算机的排版技术十分粗糙，影响到他的巨著《计算机程序设计艺术》的印刷质量，老爷子就决定自己编写一个排版软件：TeX。

用 word 写过毕业论文的都知道，word 的排版真的不是很好用，很多人应该都是用不好的，word 也有自己的宏什么的，掌握很好的话，使用起来也是很方便的，但是很多时候，鼠标的误操作就导致文本携带的编排细心丢失，而且还有各种版本的问题。至少没有什么人用 word 来编排书籍吧。但是 LaTeX 就可以很好的胜任（为什么我们学校毕业论文没有 LaTeX 模板啊，很多期刊论文都是用自己的 LaTex 模板啊）。

## 4. 高颜值的粘合剂 Eisvogel

[Eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template)，A clean pandoc LaTeX template to convert your markdown files to PDF or LaTeX。

eisvogel 通过 pandoc 把 markdown 转换成 LaTex，然后把 LaTeX 再转换成 pdf。本身 pandoc 已经可以把 markdown 或者是 LaTeX 文本转成 pdf 文本，那为什么用 eisvogel，因为 eisvogel
本身定义了 LaTeX 的模板，也固定了 markdown 标签的支持，例如 url 等等。
