const response = await fetch("/oauth/token?grant_type=authorization_code&code=" + code + "&redirect_uri=" + redirectUri, {
  method: "POST",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded",
    "oauth_type": "iam",
    "true-client-ip": "0.0.0.0",
    "Authorization": "Basic " + btoa("webtools:webtools")
  }
});
