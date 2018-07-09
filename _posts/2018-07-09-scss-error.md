---
layout: post
title:  "scss文件编译错误及配置scsslint忽略检查项"
date:   2018-07-09
excerpt: "scss文件编译错误及配置scsslint忽略检查项"
tag:
- scss
- css
---

### scss文件编译时出现错误
 * sass将scss文件编译成css文件的时候，scss文件路径不能有中文字符，否则会报错:`Encoding::CompatibilityError: incompatible character encodings: GBK and UTF-8  Use --trace for backtrace.`。
 * scss文件注释文字中如果有中文，要在文件头部加个`@charset "utf-8";`,否则会报编码错误：` error 1.scss (Line 11: Invalid GBK character "\xE6")`。

### 配置忽略检查项
scsslint可在scss文件注释中直接配置需要忽略的检查项，如：

    /* scss-lint:disable Indentation PropertySortOrder IdSelector*/
    @charset "utf-8";
