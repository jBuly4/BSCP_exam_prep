# Clickbandit
Source [here](https://portswigger.net/burp/documentation/desktop/tools/clickbandit)
To set up a Clickbandit attack:
1. Go to the top-level Burp menu and select Burp Clickbandit.
2. Click Copy Clickbandit to clipboard to copy the Clickbandit script.
3. In your browser, visit the web page that you want to test.
4. In your browser, open the developer console. This might be called Developer tools or JavaScript console.
5. Paste the Clickbandit script into the developer console, and press enter.
The Clickbandit banner appears at the top of the browser window.

Run attack:
1. Click Start to load the website.
2. Click around the site, mimicking the actions that a victim user might perform. This is recorded by Clickbandit.
3. Click Finish to complete your attack.
The target page handles clicks in the normal way. To disable this, select Disable click actions.
To avoid frame busters, select Sandbox iframe. This adds the sandbox attribute to the iframe.

Review attack
Once you have completed the attack, you can review the attack UI overlaid on the original page UI. Click the buttons on the attack UI to check that the attack works.
The following commands are available:
* Toggle transparency - Show or hide the original page UI.
* Reset - Restore the attack
* Save - Save the attack in an HTML file. This can be used as a real-world exploit of the clickjacking vulnerability.
* Use the + and - buttons to zoom in and out.
* Use your keyboard arrow keys to reposition the attack UI.