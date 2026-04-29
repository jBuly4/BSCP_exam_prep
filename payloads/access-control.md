## General info
This word [list](https://github.com/VasilyevaAn/bac-business-logic-checklist/blob/main/register_wordlist.txt) 
might be useful.


## LAB payloads
- check for robots.txt and find admin panel path
- check source code on home page and find admin panel path
- look at cookie and find Admin param, make it True
- log in -> change email -> find 302 -> change roleid and find value for admin rights
- Add X-Original-URL with value /admin
- try to upgrade user -> change POST to GET
- log in -> on my account page change id to carlos
- look at blog and find blog where author is carlos -> take his id and use it for my-account page
- log in -> change id to carlos and find that for 302 redirect there are leaked data
- log in -> change id in my account page to carlos -> find hidden password -> change id for admin
- try to download chat transcript -> change file name to the name you want
- notice that for changing role there are 2 steps -> repeat second step using non-admin cookie (in body should be 
  confirmation param) 
- change Referer header so that its value ends with /admin