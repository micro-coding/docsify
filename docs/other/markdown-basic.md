# Markdown语法(Basic)

Markdown是一种轻量级标记语言，创始人为John Gruber。它提供了一种易读易写的纯文本编写格式，本文介绍了Markdown中的基本语法。

<!--more-->

## 1. 标题

Markdown支持六级标题，书写示例如下:

```markdown
# Heading level 1
## Heading level 2
### Heading level 3
#### Heading level 4
##### Heading level 5
###### Heading level 6
```
展示效果如下：

    # Heading level 1
    ## Heading level 2
    ### Heading level 3
    #### Heading level 4
    ##### Heading level 5
    ###### Heading level 6

## 2. 段落

使用空行分割段落，连续的多个空行会被合并视为一个空行。

```markdown
First paragraph...                         //line1
                                           //line2
Second paragraph...                        //line3
                                           //line4
                                           //line5
Third paragraph...                         //line6
```
展示效果如下：

First paragraph...                         
                                           
Second paragraph...                        
                                           
                                           
Third paragraph...                         

## 3. 换行

使用两个以上空格，回车结束一行，进行换行。

```markdown
Mr. Frodo: I can't do this, Sam.(return)
Sam: I know. It's all wrong. By rights...

Mr. Frodo: I can't do this, Sam.(space space return)
Sam: I know. It's all wrong. By rights,
```
展示效果如下：

Mr. Frodo: I can't do this, Sam.
Sam: I know. It's all wrong. By rights...

Mr. Frodo: I can't do this, Sam.  
Sam: I know. It's all wrong. By rights...

## 4. 强调

### 4.1 粗体

使用双星号或双下划线加粗字体。

```markdown
Winter Is **Coming**.  
Winter Is __Coming__.
```
展示效果如下：

Winter Is **Coming**.  
Winter Is __Coming__.  

### 4.2 斜体

使用单星号或下划线倾斜字体。

```markdown
Winter Is *Coming*.  
Winter Is _Coming_.
```
展示效果如下：

Winter Is *Coming*.  
Winter Is _Coming_.

### 4.3 粗斜体

使用三个星号或下划线加粗倾斜字体。

```markdown
Winter Is ***Coming***.  
Winter Is ___Coming___.
```
展示效果如下：

Winter Is ***Coming***.  
Winter Is ___Coming___.  

## 5. 引用

### 5.1 文字引用

```markdown
>He sleeps. Although his fate was very strange, he lived.
```
展示效果如下：

>He sleeps. Although his fate was very strange, he lived.

### 5.2 多段引用

```markdown
>He sleeps. Although his fate was very strange, he lived.
>
>He died when he had no longer his angel.
```
展示效果如下：

>He sleeps. Although his fate was very strange, he lived.
>
>He died when he had no longer his angel.

### 5.3 嵌套引用

```markdown
>He sleeps. Although his fate was very strange, he lived.
>>He died when he had no longer his angel.
```

展示效果如下:

> He sleeps. Although his fate was very strange, he lived.
> 
> > He died when he had no longer his angel.

## 6. 列表

### 6.1 有序列表

有序列表使用数字接着一个英文句点，序号与数字无关，列表起始序号由第一个数字决定。

```markdown
1. First item
2. Second item
3. Third item
4. Fourth item
```

展示效果如下：

1. First item
2. Second item
3. Third item
4. Fourth item

### 6.2 无序列表

无序列表使用(*)、(+)或(-)作为列表标记。

```markdown
- First item
- Second item
+ Third item
* Fourth item
```

展示效果如下：

- First item
- Second item
+ Third item
* Fourth item
  
### 6.3 具有其它元素的列表

列表中插入的其它元素可能是段落、引用文字、代码块和图片。注意：段落、引用文字、图片需要缩进四个Space或者一个Tab；代码块需要缩进八个Space或者两个Tab。

```markdown
* This is the first list item.
    I need to add another paragraph below the second list item.
* Here's the second list item.
     > A blockquote would look great below the second list item.
* here's the third list item.
        <html>
            <head>
                <title>Test</title>
            </head>
* And here's the last.
    ![Tux, the Linux mascot](/img/tux.png)
```
展示效果如下:

1. This is the first list item.  
   
    I need to add another paragraph below the second list item.

2. Here's the second list item.

     > A blockquote would look great below the second list item.

3. here's the third list item.  
   
        <html>
          <head>
            <title>Test</title>
          </head>
          
4. And here's the last.  
    <img src="/img/NewTux.png" alt="drawing" style="width:200px;">

## 7. 代码

### 7.1 行内代码

使用(`)标记行内代码。

```markdown
`code`
```
展示效果如下:

`code`

### 7.2 代码块

使用(```)、(~~~~)或者将块的每一行缩进至少四个Space(一个Tab)创建代码块。

    ```
    1. This is a piece of code in a block.
    ```
    ~~~~
    1. This is a piece of code in a block.
    ~~~~
        1. This is a piece of code in a block.

展示效果如下:

```
1. This is a piece of code in a block.
```
~~~~
2. This is a piece of code in a block.
~~~~
    3. This is a piece of code in a block.

## 8. 水平线

使用三个及以上（*）、短划线（-）或下划线（_）创建水平线。

```markdown
***
---
___
```
展示效果如下：

***
---
___

## 9. 图像

插入图像首先需要一个感叹号（!），之后在[]中添加alt文本，在()中添加图像资源的路径或URL。Markdown允许用户在括号中的URL后面添加标题。

```markdown
![Tux, the Linux mascot](/img/NewTux.png "Tux")
```
展示效果如下:

[//]: <> (![Tux, the Linux mascot](/img/NewTux.png "Tux"))

<img src="/img/NewTux.png" alt="drawing" style="width:200px;">


## 10. 链接

创建链接,首先需要将链接文本包括在[]中，然后在()输入URL，标题可选择添加。

```markdown
[Wiki](https://zh.wikipedia.org/wiki/Wiki "维基")
```
展示效果如下:

[Wiki](https://zh.wikipedia.org/wiki/Wiki "维基")

网址和电子邮件信箱可使用尖括号包括转换为连接。

```markdown
<https://zh.wikipedia.org/>
<fake@example.com>
```
展示效果如下：

<https://zh.wikipedia.org/>  
<fake@example.com>