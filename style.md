# 样式和约定

## 一句话一行

为了在《程序员自我修养》中，让整个Markdown样式保持一致，所有文件的格式应为每行一句话。
这方便了两件事：它使使用git进行比较变得容易，并且解决了关于换行长度的问题。
例如，本段将在Markdown源文件中跨越三行。

## 链接

内部链接应为[相对链接][markdown-relative-links]，当链接到的内容属于本仓库时。

外部链接应收集在Markdown文件的底部，并用作参考链接。
见[markdown quick reference][markdown-quick-reference]中'Referenced Links'
在Markdown中使用引用链接有助于保持文件整洁有序。
这也有助于按文件更新外部链接的实践。

引用的链接应保存在有序地集合中，一个是名字部分，然后是一个内容地址部分。
为了方便使用Pandoc，可通过在Makefile的DOC_FILES变量中，链接名后加一个_N来避免重复，其中N是当前未使用的数字。

这些规则的例外情况是上下文需要URL，例如，为读者显示展示链接时。

## 示例

### 锚点

对于任意章节示例，推荐用[markdown headers][markdown-headers]来表示。

#### 例子

```markdown
## Some Topic

### Some Subheader

#### Further Subheader

##### Example

To use Further Subheader, ...

### Example

To use Some Topic, ...

```

### 内容

必要时，示例中的值可以为空或未设置，但可以包含有关此意图的注释。

在可行的情况下，示例中使用的内容和值应充分利用有关数据结构。
最常见的使用场景可能是复制并粘贴“有效示例”。
如果该示例的意图是成为一个充分被利用的示例，而不是复制粘贴的示例，则可以这样添加注释。

```markdown
### Example
```
```json
{
    "foo": null,
    "bar": ""
}
```

**vs.**

```markdown
### Example

以下是一个完整的示例（不一定要用于复制/粘贴）
```
```json
{
    "foo": [
        1,
        2,
        3
    ],
    "bar": "waffles",
    "bif": {
        "baz": "potatoes"
    }
}
```

### 链接

以下是不同类型的链接的示例。
这显示为完整的markdown文件，其中引用的链接在底部。

```markdown
读者可以通过点击在[GitHub][github]上的[程序员的自我修养手册][程序员的自我修养]，来获得更详细的了解。

程序员的自我修养官网地址是: https://devexp.top


[github]: https://github.com
[程序员的自我修养]: https://devexp.top
```


[markdown-headers]: https://help.github.com/articles/basic-writing-and-formatting-syntax/#headings
[markdown-quick-reference]: https://en.support.wordpress.com/markdown-quick-reference
[markdown-relative-links]: https://help.github.com/articles/basic-writing-and-formatting-syntax/#relative-links
