#PythonTraining
---------------


##目录
* [在列表字典集合中根据条件筛选数据](#在列表字典集合中根据条件筛选数据)
* [为元组中的每个元素命名](#为元组中的每个元素命名)
* [统计序列中元素出现的频度](#统计序列中元素出现的频度)
* [对字典进行排序](#对字典进行排序)
* [找到多个字典中的公共建](#找到多个字典中的公共建)
* [有序的字典](#有序的字典)
* [历史记录功能](#历史记录功能)


在列表字典集合中根据条件筛选数据
---------------------------------

**1.列表**
```python
data = [randint(-5, 5) for x in xrange(10)] # 生成范围在（-5,5）之间的10个数

data_2 = filter(lambda x: x >= 0, data) # 用filter函数筛选数据

data_3 = [x for x in data if x >= 0] # 用列表解析筛选数据，效率相对filter函数更高
print data
print data_2
print data_3
```

**2.字典**
```python
dict = {x: randint(60, 100) for x in xrange(1, 11)} # 生成10个值在60-100之间的数据

dict_2 = {k: v for k, v in dict.iteritems() if v >= 80} # 用字典解析筛选数据

print dict
print dict_2
```

**3.集合（类似列表）**
```python
s = {randint(-5, 5) for x in xrange(10)}

s_2 = {x for x in s if x % 3 == 0}

print s_2
```

为元组中的每个元素命名
-------------------------

**1.使用相关变量标示序号位置**
```python
NAME, AGE, SEX = xrange(3)  # 分别将0, 1, 2 分配给三个变量
student = ('shishengjia', 18, 'male')
print student[NAME], student[AGE], student[SEX]
```

**2.使用`namedtuple`(注意其开销要稍大于普通`tuple`，即上一种方法)**
```python
from collections import namedtuple

Student = namedtuple('Student', ['name', 'age', 'gender'])  # 创建实例,参数为类型名字以及各个字段名字

s = Student('shishengjia', 18, 'male')  # 创建实例
s_2 = Student(name='shishengjia', age=22, gender='male')  # 也可以这样创建

print s.name, s.age, s.gender # 访问就跟访问类的属性一样
print s_2.name, s_2.age, s_2.gender
```

namedtuple就是tuple的一个子类，在使用tuple的地方都可以使用namedtuple
```python
print isinstance(s, tuple) # True
```


统计序列中元素出现的频度
-------------------------

**1.随机序列，找到出现次数最多的3个元素**
```python
# 普通方法
from random import randint

data = [randint(0, 10) for _ in xrange(20)] 
data_2 = dict.fromkeys(data, 0) # 将随机生成的序列的值作为key，value设为0生成一个dict
for x in data:   # 统计每个元素出现的次数
    data_2[x] += 1
data_3 = sorted(data_2.iteritems(), key=lambda t: t[1], reverse=True)[:3] # 对dict根据value进行排序并取前3名
print data_3
```

```python
# 使用Counter
from random import randint
from collections import Counter

data = [randint(0, 10) for _ in xrange(20)] 
print Counter(data).most_common(3) 
```

**2.统计文章中出现次数最多的10个单词**
```python
from collections import Counter
import re

txt = open('test.txt').read()
c = Counter(re.split('\W+', txt)).most_common(10)  # re.split('\W+', txt)以非字母作为分割将文章拆散成单词组成的列表
print c
```

对字典进行排序
-------------------------
**1.先用`zip`函数将字典转化成元组列表，再进行排序、**
```python
from random import randint

d = {x: randint(60, 100) for x in 'abcxyz'}
z = zip(d.values(), d.keys()) 
print sorted(z, reverse=True)
```

**2.利用`sorted`函数的`key`参数**
```python
from random import randint

d = {x: randint(60, 100) for x in 'abcxyz'}
print sorted(d.items(), key=lambda x: x[1], reverse=True) # 取出item里的第二个值也就是value作为比较依据
```


找到多个字典中的公共建
-------------------------
```python
from random import randint, sample

# 用randin和sample函数生成三个（3-6个人分别进球1-4个球）字典
s1 = {x: randint(1, 4) for x in sample('abcdefg', randint(3, 6))}
s2 = {x: randint(1, 4) for x in sample('abcdefg', randint(3, 6))}
s3 = {x: randint(1, 4) for x in sample('abcdefg', randint(3, 6))}

#先用map函数将3个字典分别转化成建的集合，再用reduce函数依次将3个集合作与运算，即得到每轮都进球的人 
print list(reduce(lambda a, b: a & b, map(dict.viewkeys, [s1, s2, s3])))
```

有序的字典
------------
使用orderedDict来记录选手答题的结果
```python
from random import randint
from time import time
from collections import OrderedDict

players = list('abcd') # 选手姓名列表
start = time() # 开始的时间
result = OrderedDict() # 使用OrderedDict来构建有序的dict
for i in xrange(4):
    raw_input() # 阻塞，通过输入来解除
    end = time() # 结束的时间
    player = players.pop(randint(0, 3 - i)) # 随机产生一个选手，并将对应的名字从list里pop
    print i + 1, player, end - start
    result[player] = (i + 1, end - start) # 将选手作为建，排名和时间组成值进行存储
print '--------------'
for k, v in result.iteritems():
    print k, v
```

历史记录功能
---------------
猜数字游戏，记录用户之前所输入的5个数字,使用deque双端队列来记录数据
```python
from random import randint
from collections import deque
import pickle

number = randint(0, 100) # 随机生成一个数字

def guess(k):
    if k == number:
        print 'Right!'
        return True
    elif k < number:
        print '%s is less than the number' % k
    else:
        print '%s is large than the number' % k
    return False
    
# 利用pickle函数加载历史记录对象，如果没有则新建一个对象并存储
try:
    history = pickle.load(open('history'))  
except Exception:
    history = deque([], 5)  # 建立一个空的长度为5的队列
    pickle.dump(history, open('history', 'w'))
    
while True:
    line = raw_input('Plaese input a number\n')  # 用户输入
    if line.isdigit(): 
        k = int(line)
        history.append(k)  # 添加到历史记录中
        if guess(k):
            pickle.dump(history, open('history', 'w')) #  保存历史记录
            break
    elif line == 'history':  #  用户输入history，则打印记录
        print list(history)
    pickle.dump(history, open('history', 'w'))  #  异常退出也保存历史记录
```