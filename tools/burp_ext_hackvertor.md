## Install
- In order to use Hackvertor you need to open Burp Suite.
- Click the Extender tab
- Click the BApp store tab inside the Extender tab
- Scroll down and click Hackvertor
- Then click install on the right

## How-to
- click on the Hackvertor tab in the main Burp Suite window. 
- type into the input box to create some text 
to convert:
  - to base64:
    - select the text in the input box
    - click on 
    the encode tab in Hackvertor
    - find the base64 tag and click it. 
    - Hackvertor will then add the tag around the 
    selected text and the output window will show a base64 encoded string of your text.

### Examples
#### WAF bypass for SQLi
```
<storeId><@hex_entities>1 UNION SELECT username||'~'||password FROM users<@/hex_entities></storeId>
```

#### Nested tags
Hackvertor supports an unlimited amount of nesting, you can use multiple tags to encode or decode text. Hackvertor 
will **work from the inner most tag to the outer tag** and each step will be converted using the relevant tag you have 
chosen.
```
<@urlencode_all><@base64>payload</@base64></@urlencode_all>
```

#### Fast response decoding
Response -> right click -> Hackvertor -> Decode -> choose method

### Advance usage
Tags within repeater tabs:
- click the repeater tab
- right click and select the Hackvertor menu
- use any tag within the repeater tab
- tags will be displayed in the repeater window but when a request is sent 
they will be converted by Hackvertor and the server will see the converted request
- Hackvertor message editor tab: 
  - select this tab from any request tab in Burp
  - this will then create the Hackvertor interface inside a request tab, allowing to use the Hackvertor interface to 
    modify a request

## Refs
- https://github.com/hackvertor/hackvertor
- https://github.com/hackvertor/hackvertor/wiki/Tag-Store - The Tag Store is a repository of community-contributed 
  custom tags that you can browse, install, and use in Hackvertor