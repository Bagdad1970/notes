
```python
def isValid(self, s: str) -> bool:  
    mapping = {")": "(", "]": "[", "}": "{"}  

    stack = []  
    for char in s:  
        if char in mapping.values():  
            stack.append(char)  
  
        elif char in mapping.keys():  
            if not stack or stack[-1] != mapping[char]:  
                return False  

            stack.pop()  
  
    return len(stack) == 0
```