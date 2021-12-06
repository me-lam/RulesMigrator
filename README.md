# Rules Migrator

<ul><a href="#introduction">Introduction</a></ul>
<ul><a href="#usage">Usage</a></ul>
<ul><a href="#usage-exampes">Usage Examples</a></ul>
<ul><a href="#how-does-it-work">How does it work?</a></ul> 
<ul><a href="#features">Features</a></ul>


<h2>Introduction</h2>

This tool migrates PowerTrack rules from one stream to another. It uses the Rules API to get rules from a **```Source```** stream, and adds those rules to a **```Target```** stream. There is also an option to write the JSON payloads to a local file for review, and later loading into the 'Target' system.

This tool has four main use-cases:
+ Provides feedback on your version 1.0 ruleset readiness for real-time PowerTrack 2.0.
+ Clones PowerTrack version 1.0 (PT 1.0) rules to PowerTrack version 2.0 (PT 2.0).
+ Clones real-time rules to Replay streams. 
+ Clones rules between real-time streams, such as 'dev' to 'prod' streams.

If you are deploying a new PowerTrack 2.0 stream, this tool can be use to create your 2.0 ruleset, translating syntax when necessary, dropping rules when necessary, then either writing directly to the Rules API 2.0 endpoint or writing to a file for verification.
 
Given the potential high volumes of real-time Twitter data, it is a best practice to review any and all rules before adding to a live production stream. It is highly recommended that you initially build your ruleset on a non-production stream before moving to a production stream. Most Gnip customers have a development/sandbox stream deployed for their internal testing. If you have never had a 'dev' stream for development and testing, they come highly recommended. If you are migrating to PowerTrack 2.0, you have the option to use the new PowerTrack 2.0 stream as a development stream during the 30-day migration period. 

After testing your rules on your development stream, you can also use this tool to copy them to your 2.0 production stream.

For more information on migrating PowerTrack rules from one stream to another, see [this Gnip support article](http://support.gnip.com/articles/migrating-powertrack-rules.html).

The rest of this document focuses on the Ruby example app developed to migrate rules.

<br>

<h2>Usage</h2>

<br><br>

<h2>Usage Exampes</h2>


<h2>How does it work?</h2>
<ol>
	<li>Got the client's request.</li>
	<li>Search the dump for corresponded entry (request-response pair) by matching all specified request's parts:<br>
	<i>method</i>, <i>URI</i>, <i>headers</i>, and <i>body</i>.</li>
	<li>If the entry was found, the server sends the appropriate response to the client.</li>
	<li>If the entry was not found, the server sends a status <code>404</code>.</li>
</ol>
That's all.

<h2>Features</h2>

&nbsp;&nbsp;&nbsp;&nbsp;&check;&nbsp;&nbsp;no dependencies<br>
&nbsp;&nbsp;&nbsp;&nbsp;&check;&nbsp;&nbsp;no installation<br>
&nbsp;&nbsp;&nbsp;&nbsp;&check;&nbsp;&nbsp;no configs<br>
&nbsp;&nbsp;&nbsp;&nbsp;&check;&nbsp;&nbsp;crossplatform<br>
&nbsp;&nbsp;&nbsp;&nbsp;&check;&nbsp;&nbsp;single-file executable<br>
&nbsp;&nbsp;&nbsp;&nbsp;&check;&nbsp;&nbsp;command-line interface<br>
<details>
<summary>
    more features&hellip;
</summary>
<br>


<h2>
APPENDIX B.
<br>
Optional REQUEST / RESPONSE headers
</h2>
<table>
    <tr><th width="220rem">Header</th><th>Description</th></tr>
<tr></tr>
    <tr><td valign="top"><pre>X-Delay</pre>
	<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/request.svg"></a>
	<br><a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/response.svg"></a>
	</td>
    <td valign="top"><p>Request or response delay (in milliseconds).</p>
    <i>Examples:</i><br>
    <br>

Two seconds request delay:<br>
```httpx
GET / HTTP/1.1
X-Delay: 2000
```

<h2></h2>

Two seconds response delay:<br>
```httpx
HTTP/1.1 200 OK
X-Delay: 2000
```

</td></tr>
<tr></tr>
    <tr><td valign="top"><pre>X-Content-Source</pre>
	<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/request.svg"></a>
	<br><a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/response.svg"></a>
    </td>
    <td valign="top">
    The URL of the externally hosted content.<br>
    <br>
    The content from the URL will be sent as the response body.
    Supported protocols: <code>http:</code>, <code>https:</code>, <code>file:</code>, <code>data:</code>.<br>
    If the URL provides its own content type and there is no <code>Content-Type</code> header in the dump, the original <code>Content-Type</code> header received from the URL will be sent along with other response headers.
    <br>
    This header is useful when you want to send content hosted on a remote server or just send binary data as a response body.<br>
	<br>
    <i>Examples:</i>
<br><br>

Get a response body from a remote server:<br>

```httpx
HTTP/1.1 200 OK
Content-Type: application/json
X-Content-Source: http://example.com/api/car/1234.json
```

<h2></h2>

Get a response body from a file:<br>

```httpx
HTTP/1.1 200 OK
Content-Type: image/jpeg
X-Content-Source: file:///home/john/photo.jpeg
```

<h2></h2>

Get a response body from a data URI:<br>

```httpx
HTTP/1.1 200 OK
Content-Type: image/gif
X-Content-Source: data:image/gif;base64,R0lGODlhAQABAIAAAP...
```
</td></tr>
<tr></tr>
    <tr id="X-OpenAPI-Summary"><td valign="top"><pre>X-OpenAPI-Summary</pre>
	<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/request.svg"></a>
    </td>
    <td valign="top">
    <p>OpenAPI request summary text.</p>
    <i>Example:</i>

```httpx
GET /api/customer{id} HTTP/1.1
X-OpenAPI-Summary: Get customer information
```
<br>See Also:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Description"><code>X-OpenAPI-Description</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Tags"><code>X-OpenAPI-Tags</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Parameters"><code>X-OpenAPI-Parameters</code></a>

</td></tr>
<tr></tr>
    <tr id="X-OpenAPI-Description"><td valign="top"><pre>X-OpenAPI-Description</pre>
	<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/request.svg"></a>
    </td>
    <td valign="top">
    OpenAPI request description text.<br>
    <br>
    <i>Example:</i>

```httpx
GET /api/customer{id} HTTP/1.1
X-OpenAPI-Summary: Get customer information
X-OpenAPI-Description: This API extracts customer info from db
```
<br>See Also:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Summary"><code>X-OpenAPI-Summary</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Tags"><code>X-OpenAPI-Tags</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Parameters"><code>X-OpenAPI-Parameters</code></a>


</td></tr>
<tr></tr>
    <tr id="X-OpenAPI-Tags"><td valign="top"><pre>X-OpenAPI-Tags</pre>
	<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/request.svg"></a>
    </td>
    <td valign="top">
    OpenAPI request comma-separated tag list.<br>
    <br>
    <i>Example:</i>

```httpx
GET /api/customer{id} HTTP/1.1
X-OpenAPI-Summary: Get customer information
X-OpenAPI-Description: This API extracts customer info from db
X-OpenAPI-Tags: Work with customer, Buyers, Login info
```
<br>See Also:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Summary"><code>X-OpenAPI-Summary</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Description"><code>X-OpenAPI-Description</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Parameters"><code>X-OpenAPI-Parameters</code></a>

</td></tr>

<tr></tr>
    <tr id="X-OpenAPI-Parameters"><td valign="top"><pre>X-OpenAPI-Parameters</pre>
	<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/request.svg"></a>
    </td>
    <td valign="top">
    OpenAPI request parameters information.<br>
    <br>
    <i>Example:</i>

```httpx
GET /api/customer{id} HTTP/1.1
X-OpenAPI-Summary: Get customer information
X-OpenAPI-Description: This API extracts customer info from db
X-OpenAPI-Tags: Work with customer, Buyers, Login info
X-OpenAPI-Parameters: name=cust_id;description=Customer ID,
 name=rdate;description=Review Date
```
<br>See Also:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Summary"><code>X-OpenAPI-Summary</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Description"><code>X-OpenAPI-Description</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-OpenAPI-Tags"><code>X-OpenAPI-Tags</code></a>


</td></tr>

<tr></tr>
        <tr><td valign="top"><pre>X-Forward-To</pre>
		<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/response.svg"></a>
        </td>
    <td valign="top">
    Forward client request to specified origin.<br>
    <br>
    Acts as a forward proxy.<br>
    <br>
    <i>Example:</i>
<br>

```httpx
HTTP/1.1
X-Forward-To: http://example.com:8080
```
</td></tr>
<tr></tr>
        <tr id="X-Handler-CGI"><td valign="top"><pre>X-Handler-CGI</pre>
		<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/response.svg"></a>
        </td>
    <td valign="top">
    <strong>CGI</strong> (<strong>C</strong>ommon <strong>G</strong>ateway <strong>I</strong>nterface) program.<br>
    <br>
    <i>Example:</i>
<br>

```httpx
HTTP/1.1 200 OK
X-Handler-CGI: /home/john/myprog.sh param1 param2
```
<br>See Also:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-Handler-XGI"><code>X-Handler-XGI</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-Handler-JS"><code>X-Handler-JS</code></a>
</td></tr>
<tr></tr>
        <tr id="X-Handler-XGI"><td valign="top"><pre>X-Handler-XGI</pre>
		<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/response.svg"></a>
        </td>
    <td valign="top">
    <strong>XGI</strong> (<strong>E</strong>xtended <strong>G</strong>ateway <strong>I</strong>nterface) program.<br>
    <br>
XGI program is very similar to CGI, but unlike CGI, the XGI program 
reads from stdin not only the body of the request but also the first line and the headers.
In response XGI program writes <i>status line</i>, <i>headers</i> and <i>response body </i>into stdout.<br>
All CGI environment variables are also available to XGI program.<br>
    <br>
    <i>Example:</i>
<br>

```httpx
HTTP/1.1
X-XGI: /home/john/myprog.sh param1 param2
```
<br>See Also:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-Handler-CGI"><code>X-Handler-CGI</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-Handler-JS"><code>X-Handler-JS</code></a>
</td></tr>
<tr></tr>
        <tr id="X-Handler-JS"><td valign="top"><pre>X-Handler-JS</pre>
		<a href="#appendix-boptional-request--response-headers"><img src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/response.svg"></a>
        </td>
    <td valign="top">
    JavaScript response handler function.<br>
    <br>
The JavaScript functions are taken from the context files listed in the
<br>
<a href="#cli-js"><code>--js &lt;file|url&gt;...</code></a> option.<br>
<br>
The following objects are provided as handler function parameters:<br>
<ul>
	<li>
	<strong><code>request</code></strong> - <i>object</i>, <strong>READ ONLY</strong>
	<ul>
		<li><code>request.method</code>: <i>string</i> E.g. <code>request.method</code> ➞ <code>post</code></li>
		<li><code>request.path</code>: <i>string</i> E.g. <code>request.path</code> ➞ <code>/api/customers</code></li>
		<li><code>request.query</code>: <i>string</i> E.g. <code>request.query</code> ➞ <code>fname=John&lname=Doe</code></li>
		<li><code>request.parameters</code>: <i>object</i> E.g. <code>request.parameters.fname[0]</code> ➞ <code>John</code></li>
		<li><code>headers</code>: <i>object</i> E.g. <code>request.headers['content-type'][0]</code> ➞ <code>application/json</code></li>
		<li><code>request.body</code>: <i>string | object</i> E.g. <code>request.body</code> ➞ <code>{"fname": "Jonh", lname: "Doe"}</code></li>
	</ul>
	</li>
	<li>
	<strong><code>response</code></strong> - <i>object</i>, <strong>READ | WRITE</strong>
	<ul>
		<li><code>response.status</code>: <i>number</i> E.g. <code>response.status = 200</code></li>
		<li><code>response.headers</code>: <i>object</i> E.g. <code>response.headers['Content-Type'] = 'application/json'</code></li>
		<li><code>response.body</code>: <i>string | object</i> E.g. <code>response.body = {"fname": "Jonh", lname: "Doe"}</code></li>
	</ul>
	</li>
	<li><strong><code>data</code></strong>: <i>object</i>, <strong>READ | WRITE</strong> - persistent user data from the file provided by <code>--db</code> option</li>
</ul>
Among other things, the <code>X-Handler-JS</code> header allows you to modify persistent data.<br>
<br>
    <i>Examples:</i><br>
<br>

JavaScript function modify memory data:

```httpx
DELETE /customers/{id}

HTTP/1.1 200 OK
X-Handler-JS: deleteCustomer
Content-Type: application/json

{"id": "${request.parameters.id[0]}"};

```

```js
function deleteCustomer(request, response, data) {
    const id = request.parameters.id[0];
    delete data.customers[id];
}
```
<h2></h2>
JavaScript function modify memory data and provide response status, headers and body:

```httpx
DELETE /customers/{id}

HTTP/1.1
X-Handler-JS: deleteCustomer

```

```js
function deleteCustomer(request, response, data) {
    const id = request.parameters.id[0];
    if (data.customers[id] === undefined) {
        response.status = 404;
        response.body = {error: true, message: 'ID not found'};
    }
    delete data.customers[id];
    response.status = 200;
    response.headers['Content-Type'] = 'application/json';
    response.body = {error: false, message: null};
}
```

<br>See Also:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#cli-js"><code>--js &lt;file|url&gt;...</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#cli-db"><code>--db &lt;file|url&gt;...</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-Handler-CGI"><code>X-Handler-CGI</code></a><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="#X-Handler-XGI"><code>X-Handler-XGI</code></a>
<img width="1000" height="0">
</td></tr>
</table>
<br>

>**NOTE:**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. Optional request headers will **not** be sent to the server engine.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. Optional response headers will **not** be sent to clients.

<p align="right"><a href="#start"><img width="45rem" src="https://raw.githubusercontent.com/xnbox/DeepfakeHTTP/main/img/top.png"></a></p>
