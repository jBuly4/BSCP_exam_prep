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
- [Cross-site scripting](payloads/xss.md), payload status - ✅
- [Cross-site request forgery (CSRF)](payloads/csrf.md), payload status - ✅
- [Clickjacking](payloads/clickjacking.md), payload status - ✅
- [DOM-based vulnerabilities](payloads/dom-based.md), payload status - ✅
- [Cross-origin resource sharing (CORS)](payloads/cors.md), payload status - ❌
- [HTTP request smuggling](payloads/http-request-smuggling.md), payload status - ❌
- [Access control vulnerabilities](payloads/access-control.md), payload status - ❌
- [Authentication](payloads/authentication.md), payload status - ❌
- [Web cache poisoning](payloads/web-cache-poisoning.md), payload status - ❌
- [HTTP Host header attacks](payloads/host-header.md), payload status - ❌
- [OAuth authentication](payloads/oauth.md), payload status - ❌
- [JWT](payloads/jwt.md), payload status - ❌

## Stage 2 — Privilege escalation
- [SQL Injection](payloads/sqli.md), payload status - ❌
- [Cross-site scripting](payloads/xss.md), payload status - ✅
- [Cross-site request forgery (CSRF)](payloads/csrf.md), payload status - ✅
- [Clickjacking](payloads/clickjacking.md), payload status - ✅
- [DOM-based vulnerabilities](payloads/dom-based.md), payload status - ✅
- [Cross-origin resource sharing (CORS)](payloads/cors.md), payload status - ❌
- [HTTP request smuggling](payloads/http-request-smuggling.md), payload status - ❌
- [Access control vulnerabilities](payloads/access-control.md), payload status - ❌
- [Authentication](payloads/authentication.md), payload status - ❌
- [Web cache poisoning](payloads/web-cache-poisoning.md), payload status - ❌
- [HTTP Host header attacks](payloads/host-header.md), payload status - ❌
- [OAuth authentication](payloads/oauth.md), payload status - ❌
- [JWT](payloads/jwt.md), payload status - ❌

## Stage 3 — Gain secret /home/carlos/secret
- [SQL Injection](payloads/sqli.md), payload status - ❌
- [XML external entity (XXE) injection](payloads/xxe.md), payload status - ❌
- [Server-side request forgery (SSRF)](payloads/ssrf.md), payload status - ❌
- [OS command injection](payloads/command-injection.md), payload status - ❌
- [Server-side template injection](payloads/ssti.md), payload status - ❌
- [Directory traversal](payloads/directory-traversal.md), payload status - ❌
- [Insecure deserialization](payloads/insecure-deserialization.md), payload status - ❌
- [File upload vulnerabilities](payloads/file-upload.md), payload status - ❌

## Refs
1. [BSCP certification (RUS)](https://habr.com/en/companies/jetinfosystems/articles/805297/)
2. [BSCP Tech Guide](https://blog.leonardotamiano.xyz/tech/bscp-technical-guide/)
3. [Vulns combination 1](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study)
4. [Vulns combination 2](https://micahvandeusen.com/blog/burp-suite-certified-practitioner-exam-review/)
5. [PayloadAlltheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
6. 