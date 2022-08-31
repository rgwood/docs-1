# Authentication Server

You can configure the C# SDK itself as a Ditto-standard authentication service.
The server looks at the incoming credentials to decide whether to let a peer
synchronize with the server or not. You provide all of the signing and verifying
keys yourself through the SDK, thereby making the resulting JWTs ([JSON Web
Tokens](https://jwt.io/)) properly authenticate with Ditto. 

Now the web browser peer has a way to log in and sync with Ditto, in
`OnlineWithAuthentication` mode, while other devices are in SharedKey mode.

In this arrangement, the C# server peer chooses the read and write permissions
that each authenticating peer will receive. After login, if you proceed to sync
using the WebSocket transport, be aware that there is no permission control in
the reverse direction: the server peer is always granted read/write access to
all documents.

### Setup

First, you need to create three keys:

* a signing key in PEM format:

```
openssl ecparam -name prime256v1 -genkey -text | openssl pkcs8 -topk8 -nocrypt -out priv.key
```

* a verifiying key in PEM format:

```
openssl ec -in priv.key -pubout -out pub.key
```

* a [shared key](../common/security/shared-key)

### Usage

We will have two small peer Ditto instances running locally:
        
* One configured as an HTTP listener and identity provider
* One configured as a WebSocket client.

[See the full code in the Samples Repository](https://github.com/getditto/samples/tree/master/c-sharp-server)

#### Server code

The server and their other devices can use SharedKey identity while a web
browser can connect to this C# peer over LAN and sync. 

```cs
string appId = "YOUR_APP_ID_HERE";
string verifyingKey = @"-----BEGIN PUBLIC KEY-----
....
-----END PUBLIC KEY-----
";

string signingKey = @"-----BEGIN PRIVATE KEY-----
...
-----END PRIVATE KEY-----";

string license = "...";
string sharedKey = "...";

var serverIdentity = DittoIdentity.SharedKey(
    appId,
    sharedKey
);
var serverDitto = new Ditto(serverIdentity);
serverDitto.DeviceName = "TestServer";
serverDitto.SetOfflineOnlyLicenseToken(license);

// Server is an HTTP/WebSocket server only
var serverConfig = new DittoTransportConfig();
serverConfig.Listen.Http.Enabled = true;
serverConfig.Listen.Http.InterfaceIp = "127.0.0.1";
serverConfig.Listen.Http.Port = 45001;
serverConfig.Listen.Http.WebsocketSync = true;
serverConfig.Listen.Http.IdentityProvider = true;
serverConfig.Listen.Http.IdentityProviderSigningKey = signingKey;
serverConfig.Listen.Http.IdentityProviderVerifyingKeys.Add(verifyingKey);
serverDitto.SetTransportConfig(serverConfig);
try
{
    ditto.SetOfflineOnlyLicenseToken(license);
    ditto.StartSync();
}
catch (DittoException ex)
{
    Console.WriteLine("There was an error starting Ditto.");
    Console.WriteLine("Here's the following error");
    Console.WriteLine(ex.ToString());
    Console.WriteLine("Ditto cannot start sync but don't worry.");
    Console.WriteLine("Ditto will still work as a local database.");
}

// Handle any incoming authentication requests
serverDitto.DittoIdentityProviderAuthenticationRequest += (sender, args) =>
{
    Console.WriteLine("\nGot Request: ");
    Console.WriteLine(args.ThirdPartyToken);
    Console.WriteLine(args.AppId);
    if (args.AppId == appId && args.ThirdPartyToken == "jellybeans")
    {
        var success = new DittoAuthenticationSuccess();
        success.AccessExpires = DateTime.Now + new TimeSpan(1, 0, 0);
        success.UserId = "bob";
        success.ReadEverythingPermission = true;
        success.WriteEverythingPermission = true;
        args.Allow(success);
    }
    else
    {
        args.Deny();
    }
};
```

#### Client code 

Integrate this into your web application. Because it's a web browser, only the
WebSocket transport is available.


```js
const authHandler = {
    authenticationRequired: async function(authenticator) {
      console.log("Login request.");
      await authenticator.loginWithToken("jellybeans", "provider");
    },
    authenticationExpiringSoon: function(authenticator, secondsRemaining) {
      console.log(`Auth token expiring in ${secondsRemaining} seconds`)
    }
}

const identity = { 
    type: 'onlineWithAuthentication', 
    appID: "YOUR_APP_ID_HERE", 
    authHandler: authHandler, 
    enableDittoCloudSync: false, 
    customAuthURL: "http://127.0.0.1:45001" 
}

const ditto = new Ditto(identity, 'ditto')

const config = new TransportConfig()
config.connect.websocketURLs.push('ws://127.0.0.1:45001')
ditto.setTransportConfig(config)
ditto.tryStartSync()
```


## Enabling HTTPS

The Ditto Authentication server has two modes - [http and
https](https://software.ditto.live/dotnet/Ditto/1.1.10/api-reference/class_ditto_s_d_k_1_1_ditto_http_listen_config.html).
If those fields are empty (the default) then Ditto will create an HTTP listener,
and you use http:// and ws:// URLs in the JavaScript client.

This section will walk you through how to create a self-signed certificate to
set up an HTTPS authentication server on your own server or locally for
development. Another common way to have HTTPS is to make your application server
run HTTP and then use a standard reverse proxy to terminate the TLS, which is not covered by these examples.

#### 1. For development with HTTPS, you can create a self-signed certificate using `openssl`:

```bash
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out MyCertificate.crt -keyout MyKey.key
```

```bash
Generating a 4096 bit RSA private key
..............................................................................+++
..............................................+++
writing new private key to 'MyKey.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:PA
Locality Name (eg, city) []:Philadelphia
Organization Name (eg, company) [Internet Widgits Pty Ltd]:MyCompany
Organizational Unit Name (eg, section) []:MyApp
//highlight-start
Common Name (e.g. server FQDN or YOUR name) []:127.0.0.1
//highlight-end
Email Address []:admin@example.com
```

#### 2. Update your C# server code 

Set `TlsKeyPath` and `TlsCertificatePath` so that they contain paths to a valid
TLS key and certificate, then your server will become an HTTPS listener at the given port.

```cs
// Server is an HTTP/WebSocket server only
var serverConfig = new DittoTransportConfig();
serverConfig.Listen.Http.Enabled = true;
serverConfig.Listen.Http.InterfaceIp = "127.0.0.1";
serverConfig.Listen.Http.Port = 45001;
serverConfig.Listen.Http.WebsocketSync = true;
serverConfig.Listen.Http.IdentityProvider = true;
//highlight-start
serverConfig.Listen.Http.TlsKeyPath = "/path/to/MyKey.key";
serverConfig.Listen.Http.TlsCertificatePath = "/path/to/MyCertificate.crt";
//highlight-end
serverConfig.Listen.Http.IdentityProviderSigningKey = signingKey;
serverConfig.Listen.Http.IdentityProviderVerifyingKeys.Add(verifyingKey);
ditto.SetTransportConfig(serverConfig);
```

#### 3. Update your client code 

Now, use https:// and wss:// URLs in the Client SDK.

```js
const identity = {
    type: 'onlineWithAuthentication',
    appID: "YOUR_APP_ID_HERE",
    authHandler: authHandler,
    enableDittoCloudSync: false,
    //highlight-start
    customAuthURL: "https://127.0.0.1:45001"
    //highlight-end
}

const ditto = new Ditto(identity, 'ditto')

const config = new TransportConfig()
//highlight-start
config.connect.websocketURLs.push('wss://127.0.0.1:45001')
//highlight-end
ditto.setTransportConfig(config)
ditto.tryStartSync()
```

#### 4. Trust the certificate

 Visit https://127.0.0.1:45001/_ditto/auth/login and manually go through the steps to accept the cert in your browser. 

### Troubleshooting

* If you have a typo in either directory name or path, you’ll get `ERR_CONNECTION_REFUSED`
* If you have it set to the right path but with an untrusted certificate, you’ll get `ERR_CERT_INVALID`
* You get `ERR_SSL_PROTOCOL_ERROR` when the trusted certificate doesn’t match the one you’re using as the TlsCertificatePath
* You get `ERR_CERT_COMMON_NAME_INVALID` when the common name in the cert doesn’t match the url you’re using in your browser. If you're using a self-signed certificate, make sure it is trusted by your computer and the certificiate has the same “Common Name (eg, fully qualified host name)” as it’s IP address.

You can also set up your own DNS record, so you access the host via your.chosen.common.name rather than 127.0.0.1.

* Create a static record on your LAN's DNS server
* Create an entry in the [hosts file](https://en.wikipedia.org/wiki/Hosts_%28file%29)
