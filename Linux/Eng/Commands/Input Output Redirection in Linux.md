# You can completely clear a file's contents and write whatever you want into it, for example:

```bash
echo "New single line" > file.txt
```

- `echo` - a command used to print text to the console (more on this later in [[echo|echo.md]])
    
- `"New single line"` - your string
    
- `>` - this symbol redirects output to a file and **COMPLETELY** overwrites its old contents
    
- `file.txt` - this is your file
    

You can also append a line to the end of the file without deleting the old content:

```bash
echo "Another line" >> file.txt
```

Everything works the same way, except `>>` appends the line to the end of the file.
