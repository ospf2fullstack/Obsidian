---
title: Linux Windows Line Endings Fix
publish: true
---
# Line Endings
<<<<<<< Updated upstream
**CRLF is a two-character sequence used to indicate the end of a line of text in a file**. 
=======
## **CRLF 
is a two-character sequence used to indicate the end of a line of text in a file**. 
>>>>>>> Stashed changes
It is represented by the characters CR (carriage return) and LF (line feed). CRLF is used in Windows-based systems, while LF is used in Unix-based systems, such as [[Linux]]. In [[Git]], CRLF and LF are used to indicate the end of a line of text in a file.

```bash
# easy fix from the root directory
git config --global core.eol lf
```

If you want to optimize, use LF = 1 byte. CR+LF = 2 bytes. But who's counting? 

Also, escape codes: 
```bash

# LF
\n

# CR
\r
<<<<<<< Updated upstream
```
=======
```

# The Fix
## **Formatting and Whitespace**

Formatting and whitespace issues are some of the more frustrating and subtle problems that many developers encounter when collaborating, especially cross-platform. It’s very easy for patches or other collaborated work to introduce subtle whitespace changes because editors silently introduce them, and if your files ever touch a Windows system, their line endings might be replaced. Git has a few configuration options to help with these issues.

```
core.autocrlf
```

If you’re programming on Windows and working with people who are not (or vice-versa), you’ll probably run into line-ending issues at some point. This is because Windows uses both a carriage-return character and a linefeed character for newlines in its files, whereas Mac and Linux systems use only the linefeed character. This is a subtle but incredibly annoying fact of cross-platform work; many editors on Windows silently replace existing LF-style line endings with CRLF, or insert both line-ending characters when the user hits the enter key.

Git can handle this by auto-converting CRLF line endings into LF when you add a file to the index, and vice versa when it checks out code onto your filesystem. You can turn on this functionality with the core.autocrlf setting. If you’re on a Windows machine, set it to true – this converts LF endings into CRLF when you check out code:

```
$ git config --global core.autocrlf true
```

If you’re on a Linux or Mac system that uses LF line endings, then you don’t want Git to automatically convert them when you check out files; however, if a file with CRLF endings accidentally gets introduced, then you may want Git to fix it. You can tell Git to convert CRLF to LF on commit but not the other way around by setting core.autocrlf to input:

```
$ git config --global core.autocrlf input
```

This setup should leave you with CRLF endings in Windows checkouts, but LF endings on Mac and Linux systems and in the repository.

If you’re a Windows programmer doing a Windows-only project, then you can turn off this functionality, recording the carriage returns in the repository by setting the config value to false:

```
$ git config --global core.autocrlf false
```

[Source](https://stackoverflow.com/questions/5834014/lf-will-be-replaced-by-crlf-in-git-what-is-that-and-is-it-important)
>>>>>>> Stashed changes
