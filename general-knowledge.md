# General [Computer Science | Cybersecurity] knowledge

## What is Shebang (#!/path/to/env)?
- A shebang (#!) is a character sequence at the very beginning of a script file that specifies the interpreter to use for executing the script. It tells the operating system which program should process the script.
- For example:

    ```bash
    #!/bin/bash
    echo "This is a Bash script"
    ```

    ```py
    #!/usr/bin/env python
    print("This is a Python script")
    ```

    ```js
    #!/usr/bin/env node
    console.log("This is a Node.js script")
    ```

## terminal commands
- tee: read from standard input and write to standard output and files.
    - flags:
        1. -a : --append (usage: "tee -a sus.txt")
        2. -i : --ignore-interrupts
        3. -p (operate in a more appropriate MODE with pipes)


## Born again shell (Bash)
- The main difference between `scripting` and `programming languages` is that we don't need to compile the code to execute the scripting language, as opposed to programming languages.
- In general, a script does not create a process, but it is executed by the interpreter that executes the script, in this case, the Bash. To execute a script, we have to specify the interpreter and tell it which script it should process.
### `$` in Bash
- `$` itself is a symbol that indicates you're referencing a variable or special value.
When used before a variable name (e.g., $1, $domain), it retrieves the value of that variable.
Contexts for $ in Bash
- Positional Parameters:
- $1, $2, $3, ...: These refer to the arguments passed to the script (like argv[1], argv[2], ... in C).
- $#: Refers to the total number of arguments (like argc - 1 in C, except argc counts the program name too).
- $0: Refers to the script name itself.
- $@: Refers to all arguments as separate strings.
- $*: Refers to all arguments as a single string.
- Variables:
    You can define and use variables in Bash.
```bash
    name="John"
    echo $name   # Outputs "John"
```
- Special Meanings:
```bash 
    $? # Exit status of the last executed command.
    $$ # Process ID (PID) of the script.
    $! # Process ID of the last background process.
```