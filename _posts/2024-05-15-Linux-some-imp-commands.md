# some important command of linux

## Find if any file conatins some text inside.
- ****
    **eg. check if any python file contains "from flask import Flask, request, jsonify"**

```bash
grep -r --include="*.py" "from flask import Flask, request, jsonify." .
```

Breakdown of the Command
grep:

Definition: grep is a command-line utility for searching plain-text data sets for lines that match a regular expression.
Usage: It searches through text for a specified pattern and outputs lines that contain the pattern.
-r:

Definition: This option stands for recursive.
Usage: It tells grep to search through directories and their subdirectories recursively.
--include="*.co":

Definition: This option specifies a pattern for file names to include in the search.
Usage:
--include: Instructs grep to only search within files that match the specified pattern.
"*.co": The pattern to match files with a .co extension. The * is a wildcard that matches any number of characters, so it will include all files ending with .co.
"I can't answer this question.":

Definition: This is the string that grep is searching for within the files.
Usage:
The text should be enclosed in double quotes to ensure that spaces and special characters are interpreted correctly.
grep will look for this exact string in the contents of the files.
/path/to/search:

Definition: This is the path to the directory where the search should be conducted.
Usage:
Replace /path/to/search with the actual path to the directory where you want to search.
If you want to search in the current directory, you can use . (dot).