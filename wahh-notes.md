# WAHH NOTES

## Chapter 1

1. The core security problem is USERS CAN SUBMIT ARBITRARY INPUT!
2. Most developers make serious assumptions about the security of the packages that they are using to build applications.
3. Vulns in a framework affect all the places that framework exists.
4. Hardened servers and firewalls must allow http/s connections so you have a wide open attack surface to send crafted input. The data sails past the network defenses in the same way ordinary and benign data does.

## Chapter 2

### Main security mechanisms:

1. Authentication
2. Session Management
3. Access Control

### Handling User Input

1. Reject known bad – the most inferior
	2. Accept known good – Pretty good but doesn’t account for all situations (e.g. someone that needs a hyphen in their name)
	3. Sanitization – very good but effective sanitization may be difficult to achieve if several kinds of potentially malicious data need to be accommodated within one item.
2. Numerous blacklist-based filters are vulnerable to NULL byte (%00) attacks.
	1. Inserting a NULL byte anywhere before a blocked expression can cause some filters to stop processing the input.
3. SQL injection can be prevented through the correct use of parameterized queries for database access.
4. Other situations can be avoided if the application is designed not to pass user supplied input directly to the operating sytsem command interpreter.
5. Syntactic validation doesn’t help in situations where malicious input looks exactly like non-malicious input. For example, when an attacker wants to access another user’s bank account. This looks like a regular request syntactically – but is clearly malicious – This is where Boundary Validation comes in.
6. Boundary Validation
	1. Validation across trust boundaries.
	2. See page 27 for a fantastic example.
7. If sanitization isn’t being done recursively you could allow an attacker to craft something that is malicious AFTER the stripping happens (e.g. <scr<script>ipt>)
8. Canonicalization – the process of converting or decoding data into a common character set.
	1. Double URL encoding can be used in some cases to defeat. https://www.owasp.org/index.php/Double_Encoding
	2. If certain characters (even encoded ones) are stripped (non-recursively) you can use the same attack as above with <scr<script>ipt> e.g. %%2727.
	3. Explore the xss technique on p. 29.
	4. Don’t forget character mapping based on best fit p. 29. you can use characters with diacritical marks to smuggle characters past the input filters.

### Handling Attackers

1. Handling Errors
2. Maintaining audit logs
3. Alerting Administrators
4. Reacting to attacks

### Handling Errors

Errors should be handled and not dump verbose messages to the UI that an attacker could leverage to launch an attack.

### Maintaining Audit Logs

1. All events realting to the authentication functionality such as successful and failed login, and change of password
2. Key transactions, such as credit card payments and funds transfers
3. Access attempts that are blocked by the access control mechanisms
4. Any requests containing known attack strings that indicate overtly malicious intentions
5. logs may be flushed to write-once media to ensure their integrity in the event of a successful attack
6. These audit logs would provide a gold mine for any attacker.

### Alerting Admins

1. Alerts can’t be so frequent that they are ignored but roll up a lot of signatures (large number of requests from single IP, unusually large amount of funds being transferred, things that can’t normally be modified by the user unless using a proxy) and create one alert that they can react to.
Reacting to Attacks
	1. Slower responses to requests
	2. Terminate session
	3. Generally these are to frustrate the attacker to slow them down so admins can take action

## Chapter 3

1. HTTP response – Server header contains a banner indicating the web server software being used and sometimes other details such as installed modules and the server operating system. This may or MAY NOT be accurate.
2. Other HTTP methods:
	1. TRACE – returns the exOPact message it received. This can be used to detect the effect of any proxy servers between the client and serve that may manipulate the request.
	2. OPTIONS – reports the HTTP methods that are available for a particular resource. This usually lists the available methods in the Allow header.
	3. PUT – attempts to upload the specified resource to the server using the content contained in the body of the request.

### Cookies

The response Set-Cookie header can include optional attributes. One of the most interesting is HttpOnly, which means that the cookie cannot be directly accessed via client-side JavaScript.

### Status Codes

1. General
	1. 1xx – Informational
	2. 2xx – Successful
	3. 3xx – The client is redirected to a different resource
	4. 4xx – The request contains an error of some kind.
	5. 5xx – The server encountered an error fulfilling the request.
2. Specific
	1. 201 – Created – in response to a PUT request.
	2. 3xx
		1. 301 – Moved Permanently – redirects the browser permanently to a different URL, which I s specified in a Location header.
		2. 302 – Found – redirects temporarily to a URL in the Location header.
		3. 304 – Not Modified – browser to use cached copy of the requested resource. The server uses the If-Modified-Since and If-None-Match to determine if the client has the latest version of the resource.
	3. 4xx
		1. 400 – Bad Request – invalid HTTP request. Probably will encounter this if you have modified a request in certain invalid ways.
		2. 401 – Unauthorized indicates that the server requires HTTP auth before the request will be granted. The WWW-Authenticate header contains details on the type(s) of auth supported.
		3. 403 Forbidden – no one is allowed to access this.
		4. 404 Not Found
		5. 405 Method Not Allowed – indicates that the method used in the request is not supported for the specified URL.
		6. 413 Request Entity Too Large – You may see this if you are probing for buffer overflow vulnerabilities in native code. The request could be too large for the server to handle.
		7. 414 Request URI Too Long is similar to 413.
	4. 5xx
		1. 500 – Internal Server Error – these could be helpful if your request caused an unhandled exception to happen.
		2. 503 – Service Unavailable – app not responding.

### Web Functionality

1. HTTP requests can be used to send parameters in four ways.
	1. In the URL query string
	2. Cookies
	3. File path of rest URLs
	4. Body of POST requests

### Encoding

1. URLs are only permitted to contain the printable characters in the US ASCII character set – that is ASCII code in the range of 0x20 to 0x7e inclusive.
2. Problematic characters in URLs are encoded in the following ways:
	1. %3d – =
	2. %25 – %
	3. %20 – Space – could also be represented by a +
	4. %0a – New Line
	5. %00 – Null byte
3. For the purpose of attacking web applications, you should URL-encode any of the following characters when you insert them as data into an HTTP request:
	1. space % ? & = ; + #
4. 16 bit Unicode encoding starts with %u followed by the character’s Unicode code point expressed in hexadecimal.
5. UTF-8 is a variable length encoding standard.
6. Unicode encoding is primarily of interest when attacking a web application because it can be used sometimes to defeat input validation mechanisms.
7. Any character can be HTML encoded using its ASCII code in decimal form. &#34; – “ or by using its ASCII code in hexadecimal form prefixed by an x: &#x22; — "

## Chapter 4 – MAPPING THE APPLICATION

Careful when spidering as some applications don’t protect their admin actions and you could end up deleting or wrecking whole parts of an application by following actions and tossing random data at them.

>**Aside**
>Here is where looking into Damn Vulnerable Web App is important and Docker to set up the DVWA.
