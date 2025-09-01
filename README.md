"items": [
        {
          "type": "http",
          "name": "1 - GET JWT Token From Auth_Code",
          "seq": 1,
          "request": {
            "url": "https://ssm.dcloud.cl.bsch/oauth/token?grant_type=authorization_code&code=Zp2l1pM4CZ8lIy3owQIPeEfcrjg2CKP5&redirect_uri=https://chl-dss-lowcodeportal-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/",
            "method": "POST",
            "headers": [
              {
                "name": "content-type",
                "value": "application/x-www-form-urlencoded",
                "enabled": true
              },
              {
                "name": "true-client-ip",
                "value": "0.0.0.0",
                "enabled": true
              },
              {
                "name": "oauth_type",
                "value": "iam",
                "enabled": true
              }
            ],
            "params": [
              {
                "name": "grant_type",
                "value": "authorization_code",
                "type": "query",
                "enabled": true
              },
              {
                "name": "code",
                "value": "Zp2l1pM4CZ8lIy3owQIPeEfcrjg2CKP5",
                "type": "query",
                "enabled": true
              },
              {
                "name": "redirect_uri",
                "value": "https://chl-dss-lowcodeportal-dss-dev.ocp1.ch.dev.cmps.paas.f1rstbr.corp/",
                "type": "query",
                "enabled": true
              }
            ],
            "body": {
              "mode": "none",
              "formUrlEncoded": [],
              "multipartForm": []
            },
            "script": {},
            "vars": {},
            "assertions": [],
            "tests": "",
            "auth": {
              "mode": "basic",
              "basic": {
                "username": "webtools",
                "password": "webtools"
              }
            }
          }
        },
        {
