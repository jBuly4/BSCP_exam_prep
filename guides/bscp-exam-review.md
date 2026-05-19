## General thoughts
- systematize your payloads even if you won't use it. that at least will help to refresh your knowledge and be 
  familiar which payload for which case
- check and read these repos before exam - they definitely gonna help you. Then open browser tabs with them:
  - https://github.com/DingyShark/BurpSuiteCertifiedPractitioner
  - https://py-us3r.github.io/bscp-roadmap-bscproadmap/
  - https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study
- also prepare a tab with your own payloads
- study all functionality according to vuln distribution on different stages
- try harder and don't worry:
  - you have enough time
  - even if you took first vuln in 1.5 hours you have enough time to solve other 2 vulns
  - if you stuck - look to the previous point

## EXAM
### 1st APP
- Req smuggling + XSS
- BAC + cookie
- OS command injection with SSRF
#### Req smuggling + XSS
- scan specific paths (/, search function, /post/comment or something like this) - I got tentative HTTP smuggling 
  for home page and High for /post/comment
- also there was User-Agent reflection in the comment form - that means that this case is similar to lab 
  **Exploiting HTTP request smuggling to deliver reflected XSS**
- to create correct payload - use requests from scanner and modify it. For me lab payloads or from other repos 
  didn't work, so I started to modify request from scanner step by step:
  - first - identify type (CL.TE or TE.CL) and get confirmation via differential responses
  - second - trigger XSS
  - third - add payload to send your cookies to collaborator
  - fourth - use final payload to get carlos cookie

#### BAC + cookie
- pay attention to your cookie
- if you see the name of your account then it might be useful on stage 2
- here I logged in as carlos and refreshed password, but changed username to administrator
- then cookies of carlos with status **"isloggedin":true** are sent to server and in response you will get the same 
  cookie but instead of carlos - administrator
- copy it and use it => gain admin

#### OS command injection with SSRF
- on the admin page at first I deleted user expecting to gain new cookie with serialized object but got nothing
- then I found new function to work with image - but nothing in HTTP history, so I switched filtering off
- then found GET /admin-panel/metrics/blogimages?image-name=2&ImageSize=%22200x133%21%22 - that is the vector for OS 
  command injection with SSRF. example payload you may find in repos mentioned above
- I think it is also possible to find that vector using Burp Scan, but I didn't scan it... >_<

### 2nd APP
- Host header injection
- Some kind of IDOR for json roleid
- RFI