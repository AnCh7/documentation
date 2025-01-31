#### Reverse a string

```python
def reverse_string(s):
    reversed_str = ""
    for char in s:
        reversed_str = char + reversed_str  # Prepend each character to the reversed_str
    return reversed_str

input_string = "Hello, World!"
reversed_string = reverse_string(input_string)
print(reversed_string)  # Output: !dlroW ,olleH
```

#### Find 2 numbers that will sum to a number

```python
def find_two_numbers(nums, target):
    """
    This function takes a list of numbers 'nums' and a target number 'target'.
    It returns a tuple of two numbers from the list that add up to the target.
    If no such pair exists, it returns None.
    """
    seen = {}    
    for i, num in enumerate(nums):
        complement = target - num  # Calculate the complement of the current number
        if complement in seen:
            return (complement, num)  # Return the pair if complement is found
        seen[num] = i  # Add the current number to the dictionary
    return None  # Return None if no pair is found

# Example usage:
nums = [2, 7, 11, 15]
target = 9
result = find_two_numbers(nums, target)
print(result)  # Output: (2, 7)
```

