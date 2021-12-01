# Using perl-rename
These are some use-cases for perl-rename that come up quite often in my day-to-day Linux administration. If you're managing a server remotely and have a standard format for files and directories, this will help you get started. I will add to this guide over time.

### Table of Contents
- [Installing perl-rename](#installing-perl-rename)
- [Discretion is Advised](#discretion-is-advised)
- [Replace Spaces with Underscores](#replace-spaces-with-underscores)
- [Replace All Uppercase with Lowercase](#replace-all-uppercase-with-lowercase)

### Installing perl-rename
Every package manager comes with a form of perl-rename and other perl utilities, but depending on the distro, perl-rename may also be referred to as prename or even rename. Please be sure to conduct a search with your package manager before installing:

For Debian-Based Distros
```bash
apt search prename && apt search perl-rename
```

For Red Hat-Based Distros:
```bash
yum search prename && yum search perl-rename
```

For Arch-Based Distros
```bash
pacman -Ss perl-rename
```

After you have read carefully and found the package you're looking for, install like so:

For Debian-Based Distros
```bash
sudo apt install *name_of_package*
```

For Red Hat-Based Distros:
```bash
sudo yum install *name_of_package*
```

For Arch-Based Distros
```bash
sudo pacman -S *name_of_package*
```

### Discretion is Advised
Not all files and directories **should** be renamed. For the most part, the Linux/posix standard has been to keep files and directories lowercase depending on the situation, but a lot of software developers, including myself, have created dependencies on certain files being named a certain way, and renaming them can cause programs to stop working. Always be cautious about what files and directories you rename. Stay out of program directories, Linux filesystem directories, and user program directories when using perl-rename.

### Replace Spaces with Underscores
While there is an escape character for spaces, apostraphes, and other special characters, some such as myself perfer files with underscores and hyphens. I recommend underscores for spaces, and hyphens for tagging content.

In a directory filled with files that have spaces, type the following:
```bash
perl-rename 'y/ /_/' *
```

If you prefer hyphens instead of underscores altogether:
```bash
perl-rename 'y/ /-/' *
```

Remember, you can also use these commands on directories. If you wish to target specific files and directories, you can change the astiresk to the name of the file or directory in question:
```bash
perl-rename 'y/ /_/' This\ Is\ An\ Example
```

### Replace All Uppercase with Lowercase
For files that don't rely on uppercase naming conventions, it's easier to have every file and directory lowercase. When managing a server via SSH, lowercase naming conventions make traversing the filesystem convenient.

For all files in the current directory:
```bash
perl-rename 'y/A-Z/a-z/' *
```

For a specific file or directory:
```bash
perl-rename 'y/A-Z/a-z/' specific
```
