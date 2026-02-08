# [OTW] Bandit-series write up
My solutions to the Bandit-series on the learning plattform overthewire.org.

## What the Bandit-series is
overthewire.org is an online platform with interactive games for learning security concepts. The game "Bandit" is about bash-commands.
To solve a level, we need to find the password for the next one on the game-server by connecting via a secure shell connection.

## What this write up is about
This write up simply holds my personal solutions to the corresponding problems, and is simply for comparing solutions. If you plan on playing the game, you should solve the problems yourself first!



## Levels
### Bandit 0
To start the game, the first task is to connect to the server on port 2220 using ssh. Host, username and password are given. We do this by using our command line, typing:
```
$ ssh bandit0@bandit.labs.overthewire.org -p 2220
```
<br/>

### Bandit 0 -> 1
For the level one password, we simply need to read the file in the home folder.
```
$ ls
$ cat readme
```
<br/>

### Bandit 1 -> 2
The file we need to access is called "-".
```
$ cat ./-
```
<br/>

### Bandit 2 -> 3
The file is called "--spaces in this filename--".
```
$ cat ./--spaces\ in\ this\ filename--
```
<br/>

### Bandit 3 -> 4
The file is hidden inside the "inhere" folder.
```
$ ls inhere -a
```
<br/>

### Bandit 4 -> 5
The only human-readable file inside the inhere folder holds the password.
```
$ file inhere/-file0* -h
```
<br/>

### Bandit 5 -> 6
We are looking for the following properties: human-readable, 1033 byte-size, not executable. It is enough to simply use the file size.
```
$ find . -type f -size 1033c
```
To use all information given:
```
$ find . -type f -size 1033c ! -executable -exec file {} + | grep ASCII
```
<br/>

### Bandit 6 -> 7
On this one, we don't know where the password is stored. But we do know, that it is owned by user bandit7, group bandit6 and is 33 bytes in size.
```
$ find / -user bandit7 -group bandit6 -sice 33c 2>/dev/null
```
(2>/dev/null is used to filter out 'permission denied'-messages)
<br/>

### Bandit 7 -> 8
In Bandit 7, we know the password is in the (really long) data.txt file, next to the word 'millionth'.
```
$ grep millionth data.txt
```
<br/>

### Bandit 8 -> 9
Inside data.txt, the password is in the only once-occuring line.
```
$ sort data.txt | uniq -u
```
<br/>

### Bandit 9 -> 10
Password is proceeded by several '=' characters. Also the file is mostly binary.
```
$ grep == data.txt -a
```
<br/>

### Bandit 10 -> 11
The data.txt contains base64 encoded data.
```
$ base64 data.txt -d
```
<br/>

### Bandit 11 -> 12
On Bandit 11, the file to read has been encrypted in ROT13.
```
$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```
<br/>

### Bandit 12 -> 13
This one was tedious... our file hast been repeatedly compressed and then hexdumped. After reversing the hexdump, we can continuesly check the file type with -- $ file [file] --, and change its file-ending correspondingly using -- $ mv --.
```
$ xxd -r data.txt data
$ gzip -d data.gz
$ bzip2 -d data.bz2
$ gzip -d data.gz
$ tar -xf data.tar
...
```
<br/>

### TBC
