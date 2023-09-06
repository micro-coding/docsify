# Markdown语法(Extended)

本文介绍了Markdown中的扩展语法。需要注意的是：并非所有的Ｍarkdown解析器都支持扩展语法。此外，不同的解析器对于扩展语法的渲染效果也有所不同。

## 1. 表格

使用三个及以上的(-)创建表格标题，使用(|)分割列。

```markdown
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
```

展示效果如下:

First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell

通过在(---)左侧，右侧或两侧添加(:)，控制文字对齐方式。

```markdown
First Header | Second Header | Third Header
:----------- | :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right
```

展示效果如下：

First Header | Second Header | Third Header
:----------- | :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right

注意:
1. 使用[Markdown表格生成器](http://www.tablesgenerator.com/markdown_tables)可以帮助你简化创建表格。
2. 表格中的(|)需要使用HTML字符码 (\&#124;)。

## 2. 语法突出

Markdown支持语法突出显示，要实现语法突出显示，只需在(```)后添加语言名称。 

    ```Python
    x = 1
    if x == 1:
        # indented four spaces
        print("x is 1.")
    ```

展示效果如下：

```Python
x = 1
if x == 1:
    # indented four spaces
    print("x is 1.")
```
## 3. 脚注

Markdown支持添加脚注。创建脚注，首先需要在[]中添加插入符号(^)和标识符(Identifier)。标识符可以是数字、英文字母。脚注本体形式为:脚注索引＋(:　说明文本）。脚注本体可以放置在除表,块索引以及表元素之外的任何位置，按照序号自动编号。标识符在脚注索引与脚注本体之间建立关联。

```markdown
Footnotes are notes placed at the bottom of a page[^1]. They cite references or comment on a designated part of the text above it[^Myfootnote]. 

[^1]: This is the first footnote.
[^Myfootnote]: This is the second footnote.

```
展示效果如下：

Footnotes are notes placed at the bottom of a page[^1]. They cite references or comment on a designated part of the text above it[^Myfootnote]. 

[^1]: This is the first footnote.
[^Myfootnote]: This is the second footnote.

## 4.自定义标题ID

Markdown允许用户自定义标题ID，实现样式修改、页内跳转、或链接跳转至其它网页的指定位置.
```markdown
### MyHeading {#custom-id}

[MyHeading] (#custom-id)
```
展示效果如下：

    ### MyHeading {#custom-id}

[MyHeading] (#custom-id)

## 5.定义列表

定义列表的第一行为定义名称，下面几行为冒号、空格和定义。
```markdown
Foo
: Explanation of Foo.
: Another Explanation.
```
展示效果如下：

Foo
: Explanation of Foo.
: Another Explanation.

## 6.删除线

使用两个波形符号(~)前后包围需要删除的内容。

```markdown
~~Strikethrough Text~~
```
展示效果如下:

~~Strikethrough Text~~

## 7.任务列表

使用- [ ]创建任务列表,通过添加x到[ ]中选中对象。注意：符号-与[]之间留有空格。

```markdown
-[x] task one.
-[ ] task two.
-[ ] task three.
```
展示效果如下：

- [x] task one.
- [ ] task two.
- [ ] task three.

## 8.禁止自动建立链接

大多数Markdown解析器会自动将URL转换为网址链接，使用标记代码禁止网址自动转换。

```markdown
https://zh.wikipedia.org/
`https://zh.wikipedia.org/`
```
展示效果如下：

https://zh.wikipedia.org/

`https://zh.wikipedia.org/`