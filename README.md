## 用Python正则实现词频统计并验证Zipf-Law 
> ##### 注：
> - **以长篇小说[《追风筝的人》](https://baike.baidu.com/item/%E8%BF%BD%E9%A3%8E%E7%AD%9D%E7%9A%84%E4%BA%BA/3708735?fr=aladdin)（中文版为例）**
> - **Zipf-Law：**
> 在给定的语料中，对于任意一个term，其频度(freq)的排名（rank）和freq的乘积大致是一个常数。
> 推荐阅读：[Zipf and Heaps Laws’ Coefficients Depend on Language](https://www.gelbukh.com/CV/Publications/2001/CICLing-2001-Zipf.htm)
#### 导入中文分词库jieba，Counter库，matplotlib.pyplot库和re库
```javascript
import jieba
import re
import matplotlib.pyplot as plt
from collections import Counter
```
> jieba 是一个python实现的分词库，对中文有着很强大的分词能力。[git链接](https://github.com/fxsjy/jieba)
> 代码中采用的均为jieba库的[基本用法](https://blog.csdn.net/codejas/article/details/80356544)，本文不再赘述。
#### 导入打开要处理的文本追风筝的人并利用jieba分词
> 博主采用txt格式，提供一个追风筝的人.txt下载链接供参考：
> [追风筝的人.txt](http://www.xqishuta.com/Shtml28416.html)
```javascript
txt = open("追风筝的人.txt", "r", encoding="gb18030").read()
words = jieba.lcut(txt)
```
#### 去除多余的标点符号，只统计词频
```javascript
extra_characters = {"，", "。", "\n", "“", "”", "：", "；", "？", "（", "）", "！", "…"}
```
#### 遍历计数并去除多余符号
```javascript
counts = {}
for word in words:
    counts[word] = counts.get(word,0)+1
    
for word in excludes:
    del counts[word]
```
#### 返回遍历得到的键与值并排序
```javascript
items = list(counts.items())
items.sort(key=lambda x: x[1], reverse=True)
sort_list = sorted(counts.values(), reverse=True)
```
#### 将统计数据写入data.txt文本记录词频
```javascript
file = open('data.txt', mode='w')

for i in range(12138):
    word, count = items[i]
    print("{0:<10}{1:>5}".format(word,count))
    
    new_context = word + "   " + str(count) + '\n'
    file.write(new_context)

file.close()
```
#### 正则匹配结果，并将结果保存到re_match.txt
```javascript
result = open('re_match.txt', mode='w',encoding='utf-8')
#存正则匹配的数组sentences
sentences = []

#正则匹配：人物说的内容（匹配小说中带？/！的话语）
for i in re.finditer("“(.+)(\？|\！)”", txt):
    message = i.group(1)
    sentences.append(message)

#计数和并保存到re_match.txt
c = Counter(sentences)
for k, v in c.most_common(51):
    print(k, v)
    context = k + "   " + str(v) + '\n'
    result.write(context)

result.close()
```
#### 用matplotlib验证Zipf-Law并出图
```javascript
plt.title('Zipf-Law',fontsize=18)  #标题
plt.xlabel('rank',fontsize=18)     #排名
plt.ylabel('freq',fontsize=18)     #频度
plt.yticks([pow(10,i) for i in range(0,4)])  # 设置y刻度
plt.xticks([pow(10,i) for i in range(0,4)])  # 设置x刻度
x = [i for i in range(len(sort_list))]
plt.yscale('log')                  #设置纵坐标的缩放
plt.xscale('log')                  #设置横坐标的缩放
plt.plot(x, sort_list , 'r')       #绘图
plt.savefig('./Zipf_Law.jpg')      #保存图片
plt.show()
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327214349712.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzM1MzYxMg==,size_16,color_FFFFFF,t_70)
附完整代码如下，仅供参考：
```javascript
# -*- coding: utf-8 -*-
"""
@author: Alex
"""
import jieba
import re
import matplotlib.pyplot as plt
from collections import Counter

#导入打开要处理的文本追风筝的人中文版小说
txt = open("追风筝的人.txt", "r", encoding="gb18030").read()

#加入要去除的标点符号
extra_characters = {"，", "。", "\n", "“", "”", "：", "；", "？", "（", "）", "！", "…"}

#利用jieba分词
words = jieba.lcut(txt)

#设置初始字典
counts = {}

#开始遍历计数
for word in words:
    counts[word] = counts.get(word,0)+1

#去除标点符号
for word in extra_characters:
    del counts[word]

#返回遍历得分所有键与值
items = list(counts.items())
print(len(items))

#根据词出现次序进行排序
items.sort(key=lambda x: x[1], reverse=True)
#sort_list用于绘图时的数据列表
sort_list = sorted(counts.values(), reverse=True)

#将数据写入txt文本
file = open('data.txt', mode='w',encoding='utf-8')

#输出词语与词频
for i in range(12138):
    word, count = items[i]
    print("{0:<10}{1:>5}".format(word,count))

    #写入txt文件
    new_context = word + "   " + str(count) + '\n'
    file.write(new_context)

file.close()
result = open('re_match.txt', mode='w',encoding='utf-8')

#存正则匹配的数组
sentences = []

#正则匹配：人物说的内容
for i in re.finditer("“(.+)(\？|\！)”", txt):
    message = i.group(1)
    sentences.append(message)

#计数计数和并保存到re_match.txt
c = Counter(sentences)
for k, v in c.most_common(51):
    print(k, v)
    context = k + "   " + str(v) + '\n'
    result.write(context)

result.close()

#用matplotlib验证Zipf-Law
plt.title('Zipf-Law',fontsize=18)
plt.xlabel('rank',fontsize=18)  #排名
plt.ylabel('freq',fontsize=18) #频度
plt.yticks([pow(10,i) for i in range(0,4)])  # 设置y刻度
plt.xticks([pow(10,i) for i in range(0,4)])  # 设置x刻度
x = [i for i in range(len(sort_list))]
plt.yscale('log')#设置纵坐标的缩放
plt.xscale('log')
plt.plot(x, sort_list , 'r')
plt.savefig('./Zipf_Law.jpg')
plt.show()
```
