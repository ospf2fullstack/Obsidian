---
title: Linux Windows Line Endings Fix
publish: true
---
# Line Endings
**CRLF is a two-character sequence used to indicate the end of a line of text in a file**. 
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
```