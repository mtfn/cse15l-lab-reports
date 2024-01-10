# Week 1 Lab Report - Matt Fan

## `cd`
### No arguments
```
[user@sahara ~]$ cd
[user@sahara ~]$ 
```
- Run in working directory `/home`
- It appears that `cd` with no arguments changes the working directory to the home directory. That was already the current directory, so nothing changed.
- No errors

### Directory argument
```
[user@sahara ~]$ cd lecture1/messages/
[user@sahara ~/lecture1/messages]$ 
```
- Run in working directory `/home`
- The command resolved the directory path specified and changed the working directory to `/home/lecture1/messages`.
- No errors

### File argument
```
[user@sahara ~/lecture1/messages]$ cd ./en-us.txt
bash: cd: ./en-us.txt: Not a directory
[user@sahara ~/lecture1/messages]$
```
- Run in working directory `/home/lecture1/messages`
- `en-us.txt` exists in the current working directory but the command did not work because it only accepts directory paths as valid inputs. It can't change the working directory to something that is actually a file.
- Error: Not a directory. The command isn't able to resolve the path to a directory and change to it because there is a file at that location.

## `ls`
### No arguments
```
[user@sahara ~]$ ls
lecture1
[user@sahara ~]$ 
```
- Run in working directory `/home`
- It printed the contents of the current working directory, which is home. This seems to be the default behavior when no arguments are given.
- No errors

### Directory argument
```
[user@sahara ~]$ ls lecture1/messages
ar-ae.txt  en-us.txt  es-mx.txt  zh-cn.txt
[user@sahara ~]$ 
```
- Run in working directory `/home`
- It printed the contents of the folder existing at that path. When given a path to a directory, the command lists the contents in that directory but doesn't change the working directory like `cd` does.
- No errors

### File argument
```
[user@sahara ~]$ ls lecture1/messages/en-us.txt
lecture1/messages/en-us.txt
[user@sahara ~]$ 
```
- Run in working directory `/home`
- The command simply printed the argument given, the path to the file. `ls` only prints the contents of directories instead of files, and a file is given here so it just prints the path to that file.
- No errors

## `cat`
### No arguments
```
[user@sahara ~]$ cat

```
- Run in working directory `/home`
- The command doesn't print anything but also doesn't finish executing either, but with nothing specified as input there isn't anything to concatenate so no output. However, when input is typed in at this point it prints it, concatenating everything that is passed in.
- No errors

### Directory argument
```
[user@sahara ~]$ cat lecture1/messages
cat: lecture1/messages: Is a directory
[user@sahara ~]$ 
```
- Run in working directory `/home`
- `cat` takes in files or text input but doesn't work with directories because it can't read the actual text content of them.
- Error: Is a directory. It cannot handle the path to a directory `lecture1/messages` so it prints an error instead, informing the user that they provided a directory.

### File argument
```
[user@sahara ~]$ cat lecture1/messages/en-us.txt
Hello World!
[user@sahara ~]$ 
```
- Run in working directory `/home
- The command can read the contents of files, and here it is taking the path to a file and outputting the contents of that file, which are printed to the terminal.
- No errors
