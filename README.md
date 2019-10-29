# CI
continuous integration

Idea is to have CI available for all eduVPN clients and server releases. Focus is to test end-to-end if eduVPN is working. In essence it means the app should be able to sucessfully setup a VPN connection.


**Development test server**
vpn.spoor.nu

The server https://vpn.spoor.nu is "special" in that it is configured to have quickly expiring tokens/certificates:

    -browser session expires after 3 minutes (user needs to login every 3 minutes)
    -issued OAuth refresh_token expires after 3 minutes
    -generated X.509 certificates expire after 3 minutes

    -info.json 5 minutes flip: the info.json file exists 5 minutes and is than removed for another 5 minutes

   
This is to test the "happy path". All of this occurs during the normal lifetime of the VPN server, except much faster

In order to test "refresh_tokens" one has to trigger API calls between 1 minute after issuance and before 3 minutes after issuance.

The info.json 5 minutes flip is meant to check if the app somehow caches the info.json, which should not be done. **Warning:** when entering the server-url, the json file should exist. If else the app will give an error.

In addition every 15 minutes, the server is reset, it will generate a new CA, so all existing certificates will become invalid. This is to test a "full server reinstall". The applications MUST be able to recover. This "full server reset" simulates a compromised server that is reinstalled from scratch.
