# Python Lesson: Writing a Password Checker

**TeachNolen | Learning • Training • Defending**

In this beginner Python lesson, we write a simple **password length checker**. The program asks for a sample password, counts its characters, displays the recommended minimum, and calculates the difference. This lesson follows the code demonstrated in the video—**no `if` statements or conditionals**.

> **Safety note:** Use a made-up example password, not a real password. This program measures length only; it does not evaluate overall password security. If the password is longer than 12 characters, the final calculation produces a negative number because the program does not use conditional logic.

## Watch the video lesson

[![Watch TeachNolen: Writing a Password Checker](images/password-checker-thumbnail.png)](https://youtu.be/k41jO564PQY)

[Watch the lesson on YouTube](https://youtu.be/k41jO564PQY)

## Concepts in this lesson

- **Variables** — store values such as the sample password, its length, and the recommended minimum.
- **Functions** — use `input()` to receive text, `len()` to count characters, `str()` to convert numbers to text, and `print()` to display output.
- **Concatenation** — use `+` to join strings in output messages.
- **Data types** — distinguish strings (`str`) from integers (`int`).

## Build the program

### 1. Ask the user for a sample password

```python
password = input("Enter a password to check: ")
```

The `input()` function receives text from the user. The variable `password` stores that string.

### 2. Count characters and set the recommended minimum

```python
length = len(password)
minimum = 12
```

`len(password)` returns the number of characters as an integer. `minimum` stores the integer `12`.

### 3. Display the length and recommended minimum

```python
print("Your password is " + str(length) + " characters long.")
print("The recommended minimum is " + str(minimum) + " characters.")
```

`str()` converts an integer to a string so it can be joined with other strings using `+`.

### 4. Calculate and display the difference

```python
print("Characters needed to reach the minimum: " + str(minimum - length))
```

The subtraction `minimum - length` calculates the difference. This lesson deliberately does not add an `if` statement to change the output.

## Complete program from the video

```python
# Password length checker

password = input("Enter a password to check: ")
length = len(password)
minimum = 12

print("Your password is " + str(length) + " characters long.")
print("The recommended minimum is " + str(minimum) + " characters.")
print("Characters needed to reach the minimum: " + str(minimum - length))

# Make sure you save your file
```

## Practice

1. Run the program with the made-up password `abc123`. What does it print?
2. Try a sample password that is exactly 12 characters long. What is the difference?
3. Try a sample password longer than 12 characters. Why does the last line display a negative number?
4. Change `minimum` to `16`. Predict the result before running the program.

## Check your understanding

1. What does the `password` variable store?
2. What is the difference between `input()` and `print()`?
3. What data type does `len(password)` return?
4. Why do we use `str(length)` when building an output message?
5. What does `minimum - length` calculate?

[Explore the Cyberology GitHub repository](https://github.com/pinsightlabs/cyberology)

**Still Learning. Still Training. Still Defending.**
