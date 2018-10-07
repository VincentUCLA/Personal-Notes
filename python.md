# Python 偏门语法参考

## 从句

### for

```py
new_list = [expression(i) for i in old_list if filter(i)]
```

等价于

```py
new_list = []
for i in old_list:
    if filter(i):
        new_list.append(expressions(i))
```

### if

```py
condition_is_true if condition else condition_is_false
```

例子：

```py
is_fat = True
state = "fat" if is_fat else "not fat"
```

## lambda & sort cmp / key

```py
lambda [arg1 [, agr2,.....argn]] : expression
```

返回的是一个函数，举例：

```py
num.sort(cmp=lambda x, y: cmp(y+x, x+y))  # python 2 only
student_tuples = [
    ('john', 'A', 15),
    ('jane', 'B', 12),
    ('dave', 'B', 10),
]
sorted(student_tuples, key=lambda student: student[2])
```

## 字典和list的特殊用法

### filter, map

注意python 3调整了filter和map的用法，从返回一个数组变为返回一个iterator

1. filter(fun, list)：对list里每一个entry执行fun，只保留返回True的值
2. map(fun, list)：对list里每一个entry执行fun

```py
print([i for i in filter(lambda x: x % 2 != 0 and x % 3 != 0, range(2, 25))])
[5, 7, 11, 13, 17, 19, 23]
print([i for i in map(lambda x: x ** 2, range(2, 10))])
[4, 9, 16, 25, 36, 49, 64, 81]
```

### zip, enumerate

1. enumerate(list)：返回下标和内部值
2. zip(list1, list2)：返回list1和list2的值

```py
print([i for i in enumerate(range(1, 11))])
[(0, 1), (1, 2), (2, 3), (3, 4), (4, 5), (5, 6), (6, 7), (7, 8), (8, 9), (9, 10)]
print([i for i in zip(range(1, 11), [(i ** 2) for i in range(1, 11)])])
[(1, 1), (2, 4), (3, 9), (4, 16), (5, 25), (6, 36), (7, 49), (8, 64), (9, 81), (10, 100)]
```

### items, keys, values for dict

```py
params = {"key1":"value1", "key2":value2", "key3":"value3"}
params.keys()
["key1","key2","key3"]
params.values()
["value1","value2","value3"]
params.items()
[("key1","value1"),("key2","value2"),("key3","value3")]
```

### update for dict

```py
update([other])
```

Update the dictionary with the key/value pairs from other, overwriting existing keys. Return None.

## 内置数据结构

### defaultdict

和dict有两个区别：

1. value允许一个默认函数，比如`defaultdict(set)`里所有的value初始值都是一个空的`set()`
2. 不需要检测key是否储存在defaultdict里

```py
next_level = collections.defaultdict(set)
next_level[n].add(node)
```

### heapq

大名鼎鼎的priority_queue在python里的实现

```py
heapify(list)
heappush(heap, item)
heappop(heap)
```

如果要储存一个复杂对象的priority，使用tuples

```py
h = []
heappush(h, (5, 'write code'))
heappush(h, (7, 'release product'))
heappush(h, (1, 'write spec'))
heappush(h, (3, 'create tests'))
heappop(h)
(1, 'write spec')
```

heapq默认为最小堆，要改为最大堆就把tuple的priority改为负数

heapq默认提供从任意iterables里检索n个最大最小值的方法：

```py
nlargest(n, iterable, key=None)
nsmallest(n, iterable, key=None)
```

注意如果iterables里都是tuples，heapq默认把tuple第一个值视为priority

### deque

我是没看出来他跟list有啥区别……

```py
d = deque(range(5))
print(len(d))
## 输出: 5
d.popleft()
## 输出: 0
d.pop()
## 输出: 4
print(d)
## 输出: deque([1, 2, 3])
d = deque([1,2,3,4,5])
d.extendleft([0])
d.extend([6,7,8])
print(d)
## 输出: deque([0, 1, 2, 3, 4, 5, 6, 7, 8])
```

### Counter

这东西是极为好用啊，概率统计器

```py
c = Counter()                           # a new, empty counter
Counter()
c = Counter('gallahad')                 # a new counter from an iterable
Counter({'a': 3, 'l': 2, 'g': 1, 'h': 1, 'd': 1})
c = Counter({'red': 4, 'blue': 2})      # a new counter from a mapping
Counter({'red': 4, 'blue': 2})
c = Counter(cats=4, dogs=8)             # a new counter from keyword args
Counter({'dogs': 8, 'cats': 4})
```
