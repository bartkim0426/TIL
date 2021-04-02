# how to evaluate performance and time complextiy in python

with `timeit` builtin library, you can check time complextiy in concrete time.

```
import random
import timeit

print('asymptotic')
for trial in [2**_ for _ in range(1, 9)]:
    numbers = [random.randint(1, 9) for _ in range(trial)]
    m = timeit.timeit(stmt='sum = 0\nfor d in numbers:\n\tsum = sum + d', setup='import random\nnumbers = ' + str(numbers))
    print('{0:d} {1:f}'.format(trial, m)) 
```

For complex function, you can declare it with variable too.

> Don't return to measure time with full-loop.

```

setup = '''
import random
'''

asymptotic = '''
# O(n)
def asymptotic_func(array: list):
    s = set()
    result = False
    for x in array:
        if x in s:
            result = True
    return result
asymptotic_func(numbers)
'''

quadratic = '''
# O(n**n)
def quadratic_func(array: list):
    result = False
    for i in range(len(array)-1):
        for j in range(i+1, len(array)):
            if array[i] == array[j]:
                result = True
    return result
quadratic_func(numbers)
'''


print('asymptotic')
for trial in [2**_ for _ in range(1, 8)]:
    numbers = [random.randint(1, 9) for _ in range(trial)]
    m = timeit.timeit(stmt=asymptotic, setup='numbers = ' + str(numbers))
    print('{0:d} {1:f}'.format(trial, m)) 

print('quadratic')
for trial in [2**_ for _ in range(1, 8)]:
    numbers = [random.randint(1, 9) for _ in range(trial)]
    m = timeit.timeit(stmt=quadratic, setup='numbers = ' + str(numbers))
    # can use globals option instead of setup
    # m = timeit.timeit(stmt=quadratic, globals={'numbers': numbers})
    print('{0:d} {1:f}'.format(trial, m)) 
    
```

I will make a simple web for measure time/space complextiy in python code.
