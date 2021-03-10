- 深度优先搜索：

  ```python
  
  graph = {1: [2, 3], 2: [4, 5], 3: [6], 4: [7, 8], 5: [9], 8: [10, 11, 12], 10: [13, 14]}
  
  # 递归
  visited = []
  def DFS1(map, start):
      visited.append(start)
      print(start)
      if start not in graph:
          return
      for i in graph[start]:
          if i not in visited:
              DFS1(map, i)
              
  # 非递归
  def DFS2(map, start):
      stack = [start]
      visited = [start]
      print(stack[0])
      while stack:
          num = stack[-1]
          flag1 = False
          next = 0
          if num in map:
              for i in map[num]:
                  if i not in visited:
                      flag1 = True
                      next = i
                      break
          if flag1 == False:
              stack.pop()
              continue
          stack.append(next)
          visited.append(next)
          print(next)
  ```



- 广度优先搜索：

  ```python
  from queue import *
  
  graph = {1: [2, 3], 2: [4, 5], 3: [6], 4: [7, 8], 5: [9], 8: [10, 11, 12], 10: [13, 14]}
  
  def BFS(map, start):
      queue = Queue()
      queue.put(start)
      visited = [start]
      while not queue.empty():
          num = queue.get()
          print(num, " ")
          if num in map:
              for i in map[num]:
                  if i not in visited:
                      visited.append(i)
                      queue.put(i)
  ```

  

- 二分查找：

  ```python
  def binary_search(l, target):
      lo = 0
      hi = len(l) - 1
      while hi - lo > 0:
          mid = (lo + hi) // 2
          if target < l[mid]:
              hi = mid
          else:
              lo = mid + 1
      return lo - 1
  ```

  

- 冒泡排序：

  ```python
  def bubble_sort(l):
      while 1:
          ifswap = False
          for i in range(len(l) - 1):
              if l[i] > l[i + 1]:
                  l[i], l[i + 1] = l[i + 1], l[i]
                  ifswap = True
          if not ifswap:
              break
  ```

  

- 归并排序：

  ```python
  def merge_sort(l):
      if len(l) <= 1:
          return l
      mid = int(len(l) / 2)
      left = merge_sort(l[:mid])
      right = merge_sort(l[mid:])
      return merge(left, right)
  
  
  def merge(a, b):
      ans = []
      i = j = 0
      while i < len(a) and j < len(b):
          if a[i] < b[j]:
              ans.append(a[i])
              i += 1
          else:
              ans.append(b[j])
              j += 1
      if i == len(a):
          ans.extend(b[j:])
      else:
          ans.extend(a[i:])
      return ans
  ```

  

- 选择排序：

  ```python
  def select_sort(l):
      ans = []
      while len(l):
          minnum = l[0]
          for i in l:
              if i < minnum:
                  minnum = i
          ans.append(minnum)
          l.remove(minnum)
      return ans
  ```

  

- 插入排序：

  ```python
  def insert_sort(l):
      for i in range(1, len(l)):
          temp = l[i]
          j = i - 1
          while j >= 0 and temp < l[j]:
              l[j + 1] = l[j]
              j -= 1
          l[j + 1] = temp
  ```

  