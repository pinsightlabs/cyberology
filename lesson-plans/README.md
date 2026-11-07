# Python Lesson: Writing a Password Checker

**TeachNolen | Learning • Training • Defending**

In this beginner Python lesson, we write a **password length checker**. The program asks a user to enter a password, counts its characters, and tells the user how many more characters are needed to reach a minimum length.

> **Note:** This is a learning exercise, not a complete password-strength checker. A password meeting a minimum length is not necessarily secure. Don't enter a real password when testing the program.

## Watch the lesson

[![Watch TeachNolen: Python for Beginners — Writing a Password Checker](images/password-checker-thumbnail.png)](https://youtu.be/k41jO564PQY)

**[Watch the Python Password Checker video on YouTube](https://youtu.be/k41jO564PQY)**

## What you'll learn

- **Variables** — store values for use in a program.
- **Functions** — use `input()`, `len()`, and `print()` to get input, count characters, and display output.
- **Concatenation** — combine strings to build a message.
- **Data types** — work with strings (`str`) and integers (`int`).

## Build the program

### 1. Ask for a password

```python
password = input("Enter a password to check: ")
```

`input()` reads what the user types as a **string**. The variable `password` stores that string.

### 2. Set a minimum length and count characters

```python
minimum_length = 12
password_length = len(password)
```

`minimum_length` stores an **integer**. The `len()` function returns the number of characters in `password`, also as an integer.

### 3. Calculate how many more characters are needed

```python
characters_needed = minimum_length - password_length
```

Subtraction finds the difference between the minimum length and the entered password's length. A positive result means the password is too short; zero means it meets the minimum exactly; a negative result means it is longer than the minimum.

### 4. Display a message

```python
if characters_needed > 0:
    print("Your password needs " + str(characters_needed) + " more characters.")
else:
    print("Your password meets the minimum length.")
```

The `+` operator **concatenates** strings. We use `str(characters_needed)` because Python cannot concatenate a string and an integer directly. The `if` statement is an additional example of **selection**: the program chooses a message based on a condition.

## Complete example

```python
# Password length checker
minimum_length = 12

password = input("Enter a password to check: ")
password_length = len(password)
characters_needed = minimum_length - password_length

if characters_needed > 0:
    print("Your password needs " + str(characters_needed) + " more characters.")
else:
    print("Your password meets the minimum length.")
```

## Try it yourself

1. Run the program with a short **example** password, such as `abc123`.
2. Run it again with a 12-character example.
3. Change `minimum_length` to `16` and predict what will happen before running the program.
4. Explain why `str(characters_needed)` is needed when concatenating the output message.

## Check your understanding

1. What value does the variable `password` store?
2. What is the difference between `input()` and `print()`?
3. What data type does `len(password)` return?
4. What does `minimum_length - password_length` calculate?
5. What is concatenation, and where is it used in this program?

## More resources

- [Cyberology GitHub repository](https://github.com/pinsightlabs/cyberology)

**Still Learning. Still Training. Still Defending.**
