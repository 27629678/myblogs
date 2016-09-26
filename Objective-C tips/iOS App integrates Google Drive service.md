###Google API Library

####-google drive

[google-api-objectivec-client.git](https://github.com/google/google-api-objectivec-client)

[discussion group](https://groups.google.com/forum/#!forum/google-api-objectivec-client)

[wiki](https://github.com/google/google-api-objectivec-client/wiki#introduction-to-the-google-apis-client-library-for-objective-c)

####-drop box

#####1.links

[drop box sdk api v1 tutorial](https://www.dropbox.com/developers-v1/core/sdks/ios)

[drop box http api v2 documents](https://www.dropbox.com/developers/documentation/http#documentation)

#####2.knowledge

- chooser: is the fastest way to get files from Dropbox into your iOS app.
- saver: is the easiest way to add files to your users' Dropboxes. The Saver is a Drop-in component that works on web and mobile web.

all stuff above are not suitable for our application. 

-------

###Integration of DropBox

> 1. NOTE: all requests are done over **ssl**.
> 2. *Locale* global request parameter, used to specify language settings of content.

hostnames used in dropbox:

1. api.dropboxapi.com
2. content.dropboxapi.com
3. notify.dropboxapi.com

standard error code description:

- 400	Bad input parameter. Error message should indicate which one and why.
- 401	Bad or expired token. This can happen if the user or Dropbox revoked or expired an access token. To fix, you should re-authenticate the user.
- 403	Bad OAuth request (wrong consumer key, bad nonce, expired timestamp...). Unfortunately, re-authenticating the user won't help here.
- 404	File or folder not found at the specified path.
- 405	Request method not expected (generally should be GET or POST).
- 429	Your app is making too many requests and is being rate limited. 429s can trigger on a per-app or per-user basis.
- 503	If the response includes the Retry-After header, this means your OAuth 1.0 app is being rate limited. Otherwise, this indicates a transient server error, and your app should retry its request.
- 507	User is over Dropbox storage quota.
- 5xx	Server error. Check DropboxOps.

####0 oauth2 authorize

#####0.1 token

- url:https://www.dropbox.com/1/oauth2/authorize
- method:get
- parameters:
	- response_type(token|code)
	- client_id
	- redirect_uri
- returns
	- Token flow:
		- access_token
		- uid

sample response:

```
[REDIRECT_URI]#access_token=ABCDEFG&token_type=bearer&uid=12345&state=[STATE]
```



####1 account info

Retrieves information about the user's account.

- url: https://api.dropboxapi.com/1/account/info
- method: get
- parameters:
	- locale: specify language settings
- return:

```
{
    "uid": 12345678,
    "display_name": "John User",
    "name_details": {
        "familiar_name": "John",
        "given_name": "John",
        "surname": "User"
    },
    "referral_link": "https://www.dropbox.com/referrals/r1a2n3d4m5s6t7",
    "country": "US",
    "locale": "en",
    "email": "john@example.com",
    "email_verified": false,
    "is_paired": false,
    "team": {
        "name": "Acme Inc.",
        "team_id": "dbtid:1234abcd"
    },
    "quota_info": {
        "shared": 253738410565,
        "quota": 107374182400000,
        "normal": 680031877871
    }
}
```
	
####2 files get

####3 metadata

####4 delta

use delta info to update local state.

####5 thumbnails

Gets a thumbnail for an image.

- url:`https://content.dropboxapi.com/1/thumbnails/auto/<path>`
- parameters:
	- format: jpeg (default) or png.
	- size:
		- xs:32*32
		- s:64*64
		- m:128*128
		- l:640*480
		- xl:1024*768
- error:
	- 404:not found or not allowed to conversion
	- 415:the image is invalid and can not converted to a thumbanil