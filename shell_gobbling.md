# Shell Gobling
If you want to be a good sysadmin, you need to learn some basic tricks to getting the information you need out of the shell/terminal. When you have hundreds of files to oversee, you should understand how to refer to files beggining with, ending with, or containing certain characters, and you should also know some alternative ways to get the information you need from your server when gnu utils aren't working correctly or become unavailable.

### Table of contents
 - [Multiple Commands in One Line](#multiple-commands-in-one-line)
 - [](#)
 - [](#)
 - [](#)

### Multiple Commands in One Line
If you wish to be efficient with the shell, you can't rely on running every command one by one. Sometimes, you need to run commands simultaneously, or successively one after the other.

Let's say you need to copy multiple files at once from two completely different directories into your current directory. You know where both of these files are, but you don't want to leave your current directory. Let's say file1 is in /opt/ and file2 is in /home/user/documents/. Let's copy both of these files at the same time, simultaneously, in one line.
> cp /opt/file1 . ; cp /home/user/documents/file2 .

What you've just done is run two commands at the same exact time, preventing you from having to wait until one is completed before the other.
