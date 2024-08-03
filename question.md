# 2235 两数相加
```python
class Solution:
    def sum(self, num1: int, num2: int) -> int:
        return num1 + num2
```



# 2769 最大可达数
```python
class Solution:
    def theMaximumAchievableX(self, num: int, t: int) -> int:
        # num + t = x - t
        # x = num + 2t
        return num + 2 * t
```

​    
# 2651. 计算列车到站时间

```python
class Solution:
    def findDelayedArrivalTime(self, arrivalTime: int, delayedTime: int) -> int:
        # ret = arrivalTime + delayedTime
        # if ret >= 24:
        #     return ret - 24
        #     # return ret -= 24
        # return ret
        return (arrivalTime + delayedTime) % 24
    	#取模效率比四则运算低 
```

