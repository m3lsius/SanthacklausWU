# General


### Title of the challenge : Menthal Arithmetic
### Category : Programming
### Difficulty : Easy
### Creator : melsius
### Description :
      I hope you're fast

      Host 	51.75.202.113
      Port 	10001
### Files : none


## Description

This is a programming challenge. A server gives you 2 numbers, ask you to perform a mathematical calculation and you have to send back the result within 2 seconds. The main goal of this challenge is to introduce the script programming to beginners.

# Resolution

## TL;DR
The idea is to write a script (can be done in a lot of languages, here I'll do it in Python), that will connect to the server via sockets, recover the numbers, perform the calculation and send the result back to the server.

## Connect to the server

First of all, as we have an IP (51.75.202.113) and a port (10001), we will launch a netcat command to have an idea of what the server looks like.

```
$: nc 51.75.202.113 10001
```

![prompt](https://raw.githubusercontent.com/m3lsius/SanthacklausWU/master/Capture%20WU/MenthalArithmetic/prompt.png)


We can start testing the comportement of the application and we find out that, is we take more than 2 seconds to respond, we receive the message "Timeout !", and if we give the wrong answer within 2 seconds, we will receive a "Nop"

So let's write our little script in Python. First of all we need to connect to the server and receive the first prompt.

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

import socket

# creates socket object
s = socket.socket(socket.AF_INET,
                  socket.SOCK_STREAM)

host = '51.75.202.113'
port = 10001

s.connect((host, port))

print(s.recv(1024).decode()) # prompt that tells us what to do
print(s.recv(1024).decode()) # The two numbers we will use

s.close()
```

## The calculation

Now that we have our two numbers, we will need to move them into variables and then perform the calcultation using the math module
To extract the number from the strings we receive, we will use the re module.

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

import socket
import math
import re

# creates socket object
s = socket.socket(socket.AF_INET,
                  socket.SOCK_STREAM)

host = '51.75.202.113'
port = 10001

s.connect((host, port))

print(s.recv(1024).decode())
rec = s.recv(1024).decode().split("\n")

num1 =  int(re.search('(?<=: )\w+',rec[0]).group(0))
num2 =  int(re.search('(?<=: )\w+',rec[1]).group(0))

result = int(math.sqrt(num1)*pow(num2,3))
print result
s.close()
```

## Sending back the result and getting the flag

All we need to do now is to send back the result to the server, and don't forget to receive the answer (which will contain the flag)
Here is the final code for our client.py

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

import socket
import math
import re

# creates socket object
s = socket.socket(socket.AF_INET,
                  socket.SOCK_STREAM)

host = '51.75.202.113'
port = 10001

s.connect((host, port))

print("[+] Connected to 51.75.202.113 on port 10001\n")
s.recv(1024)
rec = s.recv(1024).decode().split("\n")

num1 =  re.search('(?<=: )\w+',rec[0]).group(0)
num2 =  re.search('(?<=: )\w+',rec[1]).group(0)
print("[+] The two numbers extracted are : \n\t " + num1 + "\n\t " + num2 + "\n")

result = int(math.sqrt(int(num1))*pow(int(num2),3))
print("[+] Sending the result : " + str(result) + "\n")

s.send(str.encode(str(result)))
print(s.recv(1024).decode())

s.close()
```

And the flag...

![](https://raw.githubusercontent.com/m3lsius/SanthacklausWU/master/Capture%20WU/MenthalArithmetic/flag.png)
