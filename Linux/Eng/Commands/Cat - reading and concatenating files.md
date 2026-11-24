**Its main job -- read data from one or several files (or from the keyboard) and print it as a continuous stream in the terminal.**

# Normal usage (basic scenarios)

## Quickly read a small file:
```bash
cat file.txt
```
**Output: whatever was inside the file**

## Merge several files into one:
```bash
cat part1.txt part2.txt > full.txt
```
**Output: part1 + part2, and a new file is created with this output**

## Quickly create a text file straight from the terminal (without opening nano):
Type the command, write text from the keyboard, then press Ctrl + D at the end (end-of-file signal)
```bash
cat > note.txt # Press enter here
# Then just type, and once you're done -- Ctrl + D
Hello, this is the first line
And this is the second one
```
**Output: obviously, whatever you typed**

## Insert text from the keyboard between two files (the - symbol means standard input)
```bash
cat header.txt - footer.txt > page.txt # enter
# Type your text, and press Ctrl + D, just like in the previous example
```
**Output: for example, header had top, and footer had bottom, so after this it will output:**
```text
top
your text
bottom
```
**Micro note: press enter at the end of the last line before Ctrl + D, otherwise there won't be a newline at the end of the file**

# Flags!

For example, let's imagine we have a file `test.txt` with a mix of blank lines and tabs:

```text
Hello


	Line with a tab (literally TAB, not just text BTW)
Bye
```

1. `-n` -- number all lines

Shows the number of every line (handy when you need to quickly tell a teammate or google an error on a specific config line).

```bash
cat -n test.txt
```

**Output:**

```text
     1  Hello
     2  
     3  
     4  	Line with a tab
     5  Bye
```

2. `-b` -- number non-empty lines only

Same as `-n`, but skips blank lines. Reading code and configs with this is way nicer.

```bash
cat -b test.txt
```

**Output:**

```text
     1  Hello


     2  	Line with a tab
     3  Bye
```

3. `-s` -- squeeze extra blank lines

If a file has 10 blank lines in a row, this flag squashes them into one. Ideal for reading long and poorly formatted files.

```bash
cat -s test.txt
```

**Output:**

```text
Hello

	Line with a tab
Bye
```

4. `-A` -- file "X-ray" (show all invisible characters)

The main flag for debugging when a file looks fine, yet the script refuses to run. Shows:

- `$` -- end of line (if there's a trailing space before it, you'll catch it right away).
- `^I` -- pressing `Tab` (helps tell tabs apart from spaces, e.g., in Python or YAML).
- `^M$` -- Windows line ending (if you see this, Linux might choke on the script).

```bash
cat -A test.txt
```

**Output:**

```text
Hello\$
\$
\$
^ILine with a tab\(Bye\)
```
