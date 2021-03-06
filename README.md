# pygh库介绍
## 简介
### 功能
* _目前版本暂时仅有wordcloud类功能_
### 版本更新
#### 0.0.1α1版本
* _继hyc库之后，pygh库正式发布到Pypi（目前处于α测试阶段），目前有wordcloud词云类_

__[点我跳转到GitHub界面哦~](http://github.com/fourlight/pygh)__

## 下载方法
```
pip install pygh
```

## 导入方法
### 1.
```
from pygh import art
```
### 2.
```
import pygh.art
```

### 3.
```
from pygh.art import *
```

## pygrip库内部结构
```
pygh
└─art
    └─wordcloud()类
```

## 使用方法
### wordcloud()类
* _该class类是基于jieba库（0.42.1版本）、os库、webrrowser库、pyecharts库（1.9.0版本）衍生出的一个通过txt文件制作词云的类_
#### 示例
* _注：该文件是在以下目录中运行_
```
桌面
 ├─xxxxx.txt
 └─xxxxx.py（该文件）
```
```
from pygh.art import wordcloud

# “txt=”后面填您要绘制词云的txt文件
a = wordcloud(txt='xxxxx.txt')
# 统计，ignore_num表示词云统计过程中是否忽略数字；ignore_single表示词云统计中是否忽略单个字（符）
a.count() # 两者不填默认为True
# 后两步分别为绘制与打开，name表示词云图标题
a.draw(name='xxxxx的词云统计')
a.open()
```

## 源代码
```
import jieba
class wordcloud():
def __init__(self, txt):
    with open(txt, 'r', encoding="utf-8") as f:
        text = f.read()
        self.words = jieba.lcut(text)

def add(self, word, words_dict):
    if word in words_dict:
        words_dict[word] += 1
    else:
        words_dict[word] = 1

# 统计
def count(self,
          ignore_single = True, # 忽略单个字（符）
          ignore_num = True # 忽略数字
          ):
    words_dict = {}

    drop = ['我们', '你们', '他们', '就是', '不是', '什么', '虽然', '但是', '可是', '因为', '所以', '没有', '不是', '可能', '自己']

    for word in self.words:
        if word not in drop and '一' not in word:
            # 单个字（符）检测
            single = len(word) == 1
            # 数字检测
            num = True
            try:
                int(word)
            except:
                num = False

            if ignore_num and ignore_single:
                if not single and not num:
                    self.add(word, words_dict)
            elif ignore_single and not ignore_num:
                if not single:
                    self.add(word, words_dict)
            elif ignore_num:
                if not num:
                    self.add(word, words_dict)
            else:
                self.add(word, words_dict)

    # 字典转列表
    key = list(words_dict.keys())
    value = list(words_dict.values())
    self.words_list = []
    for i in range(len(words_dict)):
        self.words_list.append((key[i], value[i]))

# 最终绘制
def draw(self, name):
    from pyecharts.charts import WordCloud
    wordcloud = WordCloud()
    wordcloud.add(series_name=name, data_pair=self.words_list)
    wordcloud.render()

# 打开
def open(self):
    import os, webbrowser
    webbrowser.open('file://' + os.path.realpath('render.html'))
