
![[Pasted image 20260417232400.png|603]]
![[Pasted image 20260417232420.png]]
![[Pasted image 20260417232437.png]]
![[Pasted image 20260417232455.png]]

```python
n = input()  
S = input()  
  
A = "0"  
inserted_index = 0  
for i, char in enumerate(S, start=1):  
    if char == 'L':  
        A = A[:inserted_index] + str(i) + A[inserted_index:]  
    elif char == 'R':  
        A = A[:inserted_index + 1] + str(i) + A[inserted_index + 1:]  
        inserted_index += 1  
  
print(A)
```