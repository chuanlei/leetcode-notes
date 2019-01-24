# ray系统学习

## 系统结构

![ray-stack](https://github.com/chuanlei/tech-notes/blob/master/pics/ray-stack.jpg)

![细节](https://github.com/chuanlei/tech-notes/blob/master/pics/ray-architecture.jpg)

![ray-workflow](https://github.com/chuanlei/tech-notes/blob/master/pics/ray-workflow.jpg)

## `ray.init()`发生了什么？

## `ray.put()`和`ray.get()`发生了什么？

## `Actor`是什么？
远程函数应该是纯函数，即：无副作用；但是这限制了Ray的表达能力。Actor是一种扩展机制，它本质上是一个有状态的worker。当新Actor初始化之后，一个新的worker会被创建，Actor中的方法会被调度到该worker上，同时可以访问worker中可变状态。声明一个Actor:

```python
@ray.remote
class Counter(object):
    def __init__(self):
        self.value = 0

    def increment(self):
        self.value += 1
        return self.value
```

创建Actor的方法为：

```python
a1 = Counter.remote()
a2 = Counter.remote()
```

使用Actor:

```python
a1.increment.remote()  # ray.get returns 1
a2.increment.remote()  # ray.get returns 1
```

完整的例子：

```python
# Create ten Counter actors.
counters = [Counter.remote() for _ in range(10)]

# Increment each Counter once and get the results. These tasks all happen in
# parallel.
results = ray.get([c.increment.remote() for c in counters])
print(results)  # prints [1, 1, 1, 1, 1, 1, 1, 1, 1, 1]

# Increment the first Counter five times. These tasks are executed serially
# and share state.
results = ray.get([counters[0].increment.remote() for _ in range(5)])
print(results)  # prints [2, 3, 4, 5, 6]
```

## `MapReduce on Ray`

`Mapper`的代码结构：

```python
@ray.remote
class Mapper(object):

    def __init__(self, title_stream):
        # Constructor, the title stream parameter is a stream of wikipedia
        # article titles that will be read by this mapper

    def get_range(self, article_index, keys):
        # Return counts of all the words with first
        # letter between keys[0] and keys[1] in the
        # articles that haven't been read yet with index
        # up to article_index
```

`Reducer`的代码结构：

```python
@ray.remote
class Reducer(object):

    def __init__(self, keys, *mappers):
         # Constructor for a reducer that gets input from the list of mappers
         # in the argument and accumulates word counts for words with first
         # letter between keys[0] and keys[1]

    def next_reduce_result(self, article_index):
         # Get articles up to article_index that haven't been read yet,
         # accumulate the word counts and return them
```

`Driver`代码：

```python
streams = # Create list of num_mappers streams
keys = # Partition the keys among the reducers.

# Create a number of mappers.
mappers = [Mapper.remote(stream) for stream in streams]

# Create a number of reduces, each responsible for a different range of keys.
# This gives each Reducer actor a handle to each Mapper actor.
reducers = [Reducer.remote(key, *mappers) for key in keys]

article_index = 0
while True:
    counts = ray.get([reducer.next_reduce_result.remote(article_index)
                      for reducer in reducers])
    article_index += 1
```

