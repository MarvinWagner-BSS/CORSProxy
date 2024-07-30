## What is this?
This is a simple Proxy server which fully bypasses [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) and allows the browser to query any API.

## Why was this made?
Many CORS proxies simply added “Access-Control-Allow-Origin” to all requests, however they did not respond to the CORS preflight requests. Without this, the API calls would still fail.

## What does this fork add?
I mainly forked this because the original repository was archived by the author, and I had some improvements to share back.<br />
(slash duplication bugfix in forwarded request, additional options for NTLM Auth passthrough, and an improved guide on how to use this)

## How does it work?
 - Responds to [CORS preflight requests](https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request).
	 1. Default header values (configurable)
		 - Access-Control-Allow-Credentials = true
		 - Access-Control-Allow-Origin = *
		 - Access-Control-Allow-Method = *
		 - Access-Control-Allow-Headers = *
		 - Access-Control-Max-Age = 86400
 - Adds [Access-Control-Allow-Origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) and [Access-Control-Allow-Credentials](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Credentials) to all responses.

## Dependencies?
Visual Studio 2022 (+?) knows whats missing and offers to download/install when you open the project solution.<br />

For deployment, it should be one of those [.NET Core 3.1 Runtimes](https://dotnet.microsoft.com/en-us/download/dotnet/3.1).<br />
(the project is build for .NET Core 3.0, but I suppose thats included in 3.1 and thats why my VS auto-installed 3.1)<br />

## How to use?
 1. Compile the solution yourself as long as there is no release.
 2. Open up the **appsettings.json** config file from the "bin" (output) directory and configure the CORS headers as required.
	 - **AllowedHosts** does nothing for now.<br />
  	   (it likely was an originally intended feature that wasn't fully implemented)
	 - **ProxyHostAddress** is the target address that requests targeting the proxy are forwarded to.<br />
	   (or you could say it is the remote address that the proxy "hosts" locally)
	 - **Access-Control-Allow-Credentials** can be defined to enable or disable NTLM Auth passthrough.
	 - **Access-Control-Allow-Origin** is the main CORS header that needs to point back to the app thats making the request.
	 - **Access-Control-Allow-Methods** must list all request methods that may be performed through the CORS proxy.
	 - **Access-Control-Allow-Headers** must list all headers that you intend to include in requests through the CORS proxy.
	 - **Access-Control-Max-Age** is available, but I haven't yet found a reason to touch it.
 3. Run **CORSProxy.exe** and the app will open up in a console window that listens on HTTP port 5000 and HTTPS port 5001.
 4. Make requests to the proxy and it will forward them to the ProxyHostAddress, and return answers with fixed CORS headers back to you.

## Additional notes
All "*" headers must eventually be made specific to one or a list of entries to solve all possible CORS issues.<br />
(at least if you also want credentials to pass through)

I'm using the following example proxy settings to successfully make NTLM authenticated CORS requests from localhost to a remote OData webservice that doesn't usually return the proper headers by itself:
 - **ProxyHostAddress** = "http://RemoteHost:RemotePort/any-optionally-restricted-subpath-you-like/or-none-at-all"
 - **Access-Control-Allow-Credentials** = "true"
 - **Access-Control-Allow-Origin** = "https://localhost"
 - **Access-Control-Allow-Methods** = "GET, POST, PATCH, PUT, DELETE, HEAD, OPTIONS"
 - **Access-Control-Allow-Headers** = "Origin, X-Requested-With, Content-Type, Accept, Access-Control-Request-Headers, Access-Control-Request-Method, If-Match"
 - **Access-Control-Max-Age** = "86400"
