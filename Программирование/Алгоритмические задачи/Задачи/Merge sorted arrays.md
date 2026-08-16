
```python
def merge(nums1: List[int], m: int, nums2: List[int], n: int) -> None:
	index1 = m - 1
	index2 = n - 1
	index = n + m - 1
	
	while index >= 0 and index2 >= 0:
		if index1 >= 0 and nums1[index1] > nums2[index2]:
			nums1[index] = nums1[index1]
			index1 -= 1
		else:
			nums1[index] = nums2[index2]
			index2 -= 1
	
		index -= 1
```