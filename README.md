# Burp Suite CP — Cheat sheet (vulnerabilities by stage)

## Tools to be prepared
- JAVA deserialization [ysoserial](github.com/frohoff/ysoserial) - cheatsheet [here](/tools/ysoserial.md) - ✅
- PHP deserialization [phpggc](github.com/ambionics/phpggc) - cheatsheet [here](/tools/phpggc.md) - ✅
- [SQLmap](github.com/sqlmapproject/sqlmap) - cheatsheet [here](/tools/sqlmap.md) - ✅
- Burp
  - [CSRF PoC](https://portswigger.net/burp/documentation/desktop/tools/engagement-tools/generate-csrf-poc)
- [JWT_tool](https://github.com/ticarpi/jwt_tool) - cheatsheet [here](/tools/jwt_tool.md)
- Might be useful:
  - [clickbandit](/tools/clickbandit.md)

## Extensions
- [Java Deserialization Scanner](https://github.com/federicodotta/Java-Deserialization-Scanner), cheatsheet [here](/tools/ysoserial.md) - ✅
- [Param Miner](https://github.com/PortSwigger/param-miner) - cheatsheet 
  [here](/tools/burp_ext_param-miner.md) - ✅
- Turbo Intruder
- [Hackvertor](https://github.com/portswigger/hackvertor) - cheatsheet 
  [here](/tools/burp_ext_hackvertor.md) - ✅
- [Request smuggler](https://github.com/portswigger/http-request-smuggler) - cheatsheet 
  [here](/tools/burp_ext_req_smuggler.md) - ✅
- [XSS cheatsheet](https://github.com/portswigger/xss-cheatsheet)
- [DOM-Invader](https://portswigger.net/burp/documentation/desktop/tools/dom-invader) - short docs [here](/tools/burp_ext_dom_invader.md) - ✅
- [JWT-editor](https://github.com/PortSwigger/jwt-editor) - cheatsheet 
  [here](/tools/burp_ext_jwt-editor.md) - ✅
- [Collaborator everywhere](https://github.com/portswigger/collaborator-everywhere-v2) - ✅

## Useful links and wordlists
- Great [repo](https://github.com/swisskyrepo/PayloadsAllTheThings)
- XSS cheatsheet [link](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
- SQL cheatsheet [link](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- User list [link](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- Password list [link](https://portswigger.net/web-security/authentication/auth-lab-passwords)
- JWT secrets list [link](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list)
- XML DTD finder [link](https://github.com/GoSecure/dtd-finder)
- repo for [SSTI cheatsheet](https://github.com/Hackmanit/template-injection-table) and [cheatsheet](https://cheatsheet.hackmanit.de/template-injection-table/) itself

## Stage 1 — Gain access
- [Cross-site scripting](payloads/xss.md), payload status - ✅
- [Cross-site request forgery (CSRF)](payloads/csrf.md), payload status - ✅
- [Clickjacking](payloads/clickjacking.md), payload status - ✅
- [DOM-based vulnerabilities](payloads/dom-based.md), payload status - ✅
- [Cross-origin resource sharing (CORS)](payloads/cors.md), payload status - ✅
- [HTTP request smuggling](payloads/http-request-smuggling.md), payload status - ✅
- [Access control vulnerabilities](payloads/access-control.md), payload status - ✅
- [Authentication](payloads/authentication.md), payload status - ✅
- [Web cache poisoning](payloads/web-cache-poisoning.md), payload status - ✅
- [HTTP Host header attacks](payloads/host-header.md), payload status - ✅
- [OAuth authentication](payloads/oauth.md), payload status - ✅ but SSRF labs are ❌
- [JWT](payloads/jwt.md), payload status - ✅

## Stage 2 — Privilege escalation
- [SQL Injection](payloads/sqli.md), payload status - ✅, sqlmap only for practice exam, labs - ❌
- [Cross-site scripting](payloads/xss.md), payload status - ✅
- [Cross-site request forgery (CSRF)](payloads/csrf.md), payload status - ✅
- [Clickjacking](payloads/clickjacking.md), payload status - ✅
- [DOM-based vulnerabilities](payloads/dom-based.md), payload status - ✅
- [Cross-origin resource sharing (CORS)](payloads/cors.md), payload status - ✅
- [HTTP request smuggling](payloads/http-request-smuggling.md), payload status - ✅
- [Access control vulnerabilities](payloads/access-control.md), payload status - ✅
- [Authentication](payloads/authentication.md), payload status - ✅
- [Web cache poisoning](payloads/web-cache-poisoning.md), payload status - ✅
- [HTTP Host header attacks](payloads/host-header.md), payload status - ✅
- [OAuth authentication](payloads/oauth.md), payload status - ✅
- [JWT](payloads/jwt.md), payload status - ✅

## Stage 3 — Gain secret /home/carlos/secret
- [SQL Injection](payloads/sqli.md), payload status - ✅, sqlmap only for practice exam, labs - ❌
- [XML external entity (XXE) injection](payloads/xxe.md), payload status - ✅
- [Server-side request forgery (SSRF)](payloads/ssrf.md), payload status - ✅
- [OS command injection](payloads/command-injection.md), payload status - ✅ but OAST labs are ❌
- [Server-side template injection](payloads/ssti.md), payload status - ✅
- [Directory/Path traversal](payloads/path-traversal.md), payload status - ✅
- [Insecure deserialization](payloads/insecure-deserialization.md), payload status - ✅
- [File upload vulnerabilities](payloads/file-upload.md), payload status - ✅

## Refs
1. [BSCP certification (RUS)](https://habr.com/en/companies/jetinfosystems/articles/805297/)
2. [BSCP Tech Guide](https://blog.leonardotamiano.xyz/tech/bscp-technical-guide/)
3. [Vulns combination 1](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study)
4. [Vulns combination 2](https://micahvandeusen.com/blog/burp-suite-certified-practitioner-exam-review/)
5. [Vulns combination 3](https://github.com/DingyShark/BurpSuiteCertifiedPractitioner)
6. [PayloadAlltheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
7. [Req Smuggling](https://medium.com/@muhammadosama0121/http-request-smuggling-f28485cd53dd)
8. [BAC (RUS)](https://github.com/VasilyevaAn/bac-business-logic-checklist)
9. Good [BSCP repo with labs]( https://github.com/frank-leitner/portswigger-websecurity-academy)
10. [Discover hidden attack surface](https://portswigger.net/research/cracking-the-lens-targeting-https-hidden-attack-surface)