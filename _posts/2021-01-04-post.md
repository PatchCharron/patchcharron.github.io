---
title: Test Azure Active Directory Enterprise Apps
layout: post
---
I have had a lot of clients lately intergrade Azure with a third-party solution, like Cloud Checker, or Prisma Cloud, etc. These services are great but they require the configuration of an Azure Active Directory Enterprise app to setup permissions. I have had a couple of issues lately where a product (none of the above) struggle to connect and I open a support ticket with the solution provider. Their first questions are around the configuration of the app. After the second time I wrote this quick PowerShell script to login as the app and list the subscriptions. $url can be changed to whatever API call you need to check.

I found this type of thing to not be well documented, so after some trial and error I came up with this; its great for a quick troubleshoot or for some advanced scripting.

```powershell
$app = ""
$tenant = ""
$key = ""


$tokenEndpoint = {https://login.microsoftonline.com/{0}/oauth2/token} -f $tenant

$arm = "https://management.core.windows.net/"

$Body = @{
    'resource'=$arm
    'client_id' = $app
    'grant_type' = 'client_credentials'
    'client_secret' = $key
}

$params1 = @{
    ContentType = 'application/x-www-form-urlencoded'
    Headers = @{'accept'='application/json'}
    Body = $Body
    Method = 'Post'
    URI = $tokenEndpoint
}

$token = Invoke-RestMethod @params1

$Headers = @{}

$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")

$urlPrefix = "https://management.azure.com/"

$url = ($urlPrefix + "subscriptions?api-version=2019-05-01")
(Invoke-RestMethod -Uri $url -Headers $headers).value
```