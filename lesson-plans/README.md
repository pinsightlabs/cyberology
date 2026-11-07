# Python for Beginners: Writing a Password Length Checker

A TeachNolen beginner Python lesson. In this activity, we'll write a program that asks for a password, counts its characters, and calculates how many characters are needed to reach a recommended minimum of 12.

## Watch the video lesson

[![Watch TeachNolen: Writing a Password Checker](https://img.youtube.com/vi/k41jO564PQY/hqdefault.jpg)](https://youtu.be/k41jO564PQY)

[Watch the lesson on YouTube](https://youtu.be/k41jO564PQY)

## Concepts we'll use

- **Variables:** Store values for use in a program.
- **Functions:** `input()` collects text, `len()` counts characters, `str()` converts a number to text, and `print()` displays output.
- **Concatenation:** Joins strings using `+`.
- **Data types:** User input is a string; character counts and the minimum length are integers.

## Write the program

Create a Python file and enter the following code, as demonstrated in the video:

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

## Understand the code

1. `input()` prompts the user to enter a password and stores the text in `password`.
2. `len(password)` counts the characters and stores the result in `length`.
3. `minimum = 12` stores the target length as an integer.
4. `str()` converts numeric results to strings so they can be concatenated with the messages in `print()`.
5. `minimum - length` calculates the difference between the target length and the password length.
6. Lines beginning with `#` are comments; Python does not execute them.

## Try it

Run the program using a password shorter than 12 characters, one exactly 12 characters long, and one longer than 12 characters. Observe how the last line changes.

**Important:** This introductory program checks length only. It does not determine password strength or securely handle real passwords. Because the code has no conditional statement, passwords longer than 12 characters produce a negative number for “characters needed.” We leave that behavior unchanged to match the video.

## Check your understanding

1. What does the `password` variable store?
2. What does `len(password)` return?
3. Why do we use `str(length)` inside the `print()` statement?
4. What does the `+` operator do when joining strings?
5. What does `minimum - length` calculate?

---

**TeachNolen** — Learning • Training • Defending

[Cyberology GitHub repository](https://github.com/pinsightlabs/cyberology)
