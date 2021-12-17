# Code-diary
个人自学代码踩坑实录
无干货
全是error！

###  主要记录内容
- **插入图片**
- **代码记录**
- Markdown用法
### 插入图片
![示例图片](./FILES/readme.md/微信图片_20211206171542.jpg)
- 这种代码不可以调整图片大小
- 不可以调整图片位置
<img src=./FILES/readme.md/微信图片_20211206171542.jpg width="100" alt="图片描述文字">

- 这种可以调整大小，如果改变位置需要被< div >包围
### 代码记录
- 短代码-->`print('Hello world')`
- 代码块-->可以直接复制
```python
import pandas
import numpy
print('Hello world')
```
### Markdown用法
#### 常规使用
- 常用符号‘-’，进入list
- 常用符号‘#’，进入标题
- 常用符号‘`’，进入代码，单行一个，代码块3个（记得换行，可以指定语言）
- 常用符号‘_’,‘*’，粗体bold，斜体italic
- 常用符号‘>’，引用，需要另起一行
- 可以输入英文自动匹配, 加粗bold，列表list，斜体Italic
- 所有字符匹配需要满足空格和换行的条件
#### 高级应用
- 链接的使用
  [软件代码源码git](https://github.com/purocean/yn)，注意需要http，直接手打www开头无法跳转,可以直接link关键词输入
- 目录的使用,关键词toc
  [toc]{type: "ul", level: [2,3,4]} 
