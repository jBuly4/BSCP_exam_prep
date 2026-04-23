# Burp Suite CP — Cheat sheet (vulnerabilities by stage)

## Tools to be prepared
- JAVA deserialization [ysoserial](github.com/frohoff/ysoserial) - cheatsheet [here](/tools/ysoserial.md)
- PHP deserialization [phpggc](github.com/ambionics/phpggc) - cheatsheet [here](/tools/phpggc.md)
- [SQLmap](github.com/sqlmapproject/sqlmap) - cheatsheet [here](/tools/sqlmap.md)
- Burp
  - [CSRF PoC](https://portswigger.net/burp/documentation/desktop/tools/engagement-tools/generate-csrf-poc)
- [JWT_tool](https://github.com/ticarpi/jwt_tool) - cheatsheet [here](/tools/jwt_tool.md)
- Might be useful:
  - [clickbandit](/tools/clickbandit.md)

## Extensions
- [JWT-editor](https://github.com/PortSwigger/jwt-editor) - cheatsheet 
  [here](/tools/burp_ext_jwt-editor.md)
- [Param Miner](https://github.com/PortSwigger/param-miner) - cheatsheet 
  [here](/tools/burp_ext_param-miner.md)
- Turbo Intruder
- [Hackvertor](https://github.com/portswigger/hackvertor) - cheatsheet 
  [here](/tools/burp_ext_hackvertor.md)
- [Request smuggler](https://github.com/portswigger/http-request-smuggler) - cheatsheet 
  [here](/tools/burp_ext_req_smuggler.md)
- [XSS cheatsheet](https://github.com/portswigger/xss-cheatsheet)

## Useful links and wordlists
- XSS cheatsheet [link](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
- SQL cheatsheet [link](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- User list [link](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- Password list [link](https://portswigger.net/web-security/authentication/auth-lab-passwords)
- JWT secrets list [link](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list)


## Stage 1 — Gain access
- [Cross-site scripting](payloads/xss.md)
- [Cross-site request forgery (CSRF)](payloads/csrf.md)
- [Clickjacking](payloads/clickjacking.md)
- [DOM-based vulnerabilities](payloads/dom-based.md)
- [Cross-origin resource sharing (CORS)](payloads/cors.md)
- [HTTP request smuggling](payloads/http-request-smuggling.md)
- [Access control vulnerabilities](payloads/access-control.md)
- [Authentication](payloads/authentication.md)
- [Web cache poisoning](payloads/web-cache-poisoning.md)
- [HTTP Host header attacks](payloads/host-header.md)
- [OAuth authentication](payloads/oauth.md)
- [JWT](payloads/jwt.md)

## Stage 2 — Privilege escalation
- [SQL Injection](payloads/sqli.md)
- [Cross-site scripting](payloads/xss.md)
- [Cross-site request forgery (CSRF)](payloads/csrf.md)
- [Clickjacking](payloads/clickjacking.md)
- [DOM-based vulnerabilities](payloads/dom-based.md)
- [Cross-origin resource sharing (CORS)](payloads/cors.md)
- [HTTP request smuggling](payloads/http-request-smuggling.md)
- [Access control vulnerabilities](payloads/access-control.md)
- [Authentication](payloads/authentication.md)
- [Web cache poisoning](payloads/web-cache-poisoning.md)
- [HTTP Host header attacks](payloads/host-header.md)
- [OAuth authentication](payloads/oauth.md)
- [JWT](payloads/jwt.md)

## Stage 3 — Gain secret /home/carlos/secret
- [SQL Injection](payloads/sqli.md)
- [XML external entity (XXE) injection](payloads/xxe.md)
- [Server-side request forgery (SSRF)](payloads/ssrf.md)
- [OS command injection](payloads/command-injection.md)
- [Server-side template injection](payloads/ssti.md)
- [Directory traversal](payloads/directory-traversal.md)
- [Insecure deserialization](payloads/insecure-deserialization.md)
- [File upload vulnerabilities](payloads/file-upload.md)

## Refs
1. [BSCP certification (RUS)](https://habr.com/en/companies/jetinfosystems/articles/805297/)
2. [BSCP Tech Guide](https://blog.leonardotamiano.xyz/tech/bscp-technical-guide/)
3. [Vulns combination 1](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study)
4. [Vulns combination 2](https://micahvandeusen.com/blog/burp-suite-certified-practitioner-exam-review/)
5. [PayloadAlltheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
6. 