# Shell Gobling
If you want to be a good sysadmin, you need to learn some basic tricks to getting the information you need out of the shell/terminal. When you have hundreds of files to oversee, you should understand how to refer to files beggining with, ending with, or containing certain characters, and you should also know some alternative ways to get the information you need from your server when gnu utils aren't working correctly or become unavailable.

### Table of contents
 - [Multiple Commands in One Line](#multiple-commands-in-one-line)
 - [Alternatives to cd and ls](#alternatives-to-cd-and-ls)
 - [File Gobbling](#file-gobbling)
 - [Conclusion](#conclusion)

### Multiple Commands in One Line
If you wish to be efficient with the shell, you can't rely on running every command one by one. Sometimes, you need to run commands simultaneously, or successively one after the other.

Let's say you need to copy multiple files at once from two completely different directories into your current directory. You know where both of these files are, but you don't want to leave your current directory. Let's say file1 is in /opt/ and file2 is in /home/user/documents/. Let's copy both of these files at the same time, simultaneously, in one line.
```bash
cp /opt/file1 . ; cp /home/user/documents/file2 .
```

What you've just done is run two commands at the same exact time, preventing you from having to wait until one is completed before the other.

Now let's say you need to run one command after the other in the same line. Let's say you want to make a directory called temp, change directory to temp, and then copy a big file from /bin/ called myProgram into temp all in one line. You can't use the semicolon, because these commands will run asynchronously, meaning you won't know which one runs or gets completed first, which in this case, will cause errors.

Let's do it all in one shot successively using binary ampersand, &&.
```bash
mkdir temp && cd temp && cp /bin/myProgram .
```

When using binary ampersand, commands are run in the order you specify, and if one command fails, the rest are not executed. When using a semicolon, it doesn't matter if one of the commands fails, the other commands are still running. With ampersand, if the current command being run fails, the next set of commands aren't going to be run. In most cases, it's better to use ampersand than semicolon.

### Alternatives to cd and ls
It's not always useful, but given something went wrong with your gnu coreutils package, you might have to use alternatives to cd (change directory) and ls (list).

You can use echo to list all files in a directory:
```bash
echo *
```

You can use pushd to change directories:
```bash
pushd ~
```

This example changes your current directory to the home directory. There may be some use to this, for example, if you installed an instance of Linux that, for some reason, lacked coreutils. It happens.

### File Gobbling
Let's say you have the following files in a directory:
```bash
filea
fileb
filec
FileA
FileB
FileC
file1
file2
file3
File11
File12
File13
```

Let's say we need to list groups of these files based on certain characters and capitlization; you can make these files using the *touch* command and follow along yourself:

To list all files that start with lowercase f:
```bash
ls file*
```

or

```bash
ls f*
```

To list all files that start with uppercase F:
```bash
ls File*
```

or

```bash
ls F*
```

Those were the easy ones, now let's say we need to list all files ending with numbers:
```bash
ls *[0-9]
```

This is a range. Anything between [ and ] is considered a range, and we can do the same with letters, too.

Let's only list files ending in letters a-z:
```bash
ls *[a-z]
```

But you'll notice this only printed files ending with lowercase letters.

Here's how to print both files ending with both uppercase and lowercase:
```bash
ls *[a-z] ; ls *[A-Z]
```

And if you just wanted files ending in uppercase, then you would just type in the second part of the previous command.

Now what if you wanted to print out files beggining with f or F and ending in numbers:
```bash
ls [fF]*[0-9]
```

Let's say you wanted to print all files either ending in 1 or 3:
```bash
ls *[13]
```

And let's say you just want to print out files ending with a or A:
```bash
ls *[aA]
```

Finally, let's print out all files not ending in 3:
```bash
ls *[!3]
```

### Conclusion
I hope that this guide provided some quick use to you. Knowing everything I taught you above, you should now be able to start coming up with new use-cases for these tactics on different scenarios, and possible come up with your own combination of commands that serve useful.

If you have any questions, as always, feel free to email me at nickolas@nliaquin.xyz
