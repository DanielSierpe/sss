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




        curl --request POST \
  --url 'https://ssm.dcloud.cl.bsch/oauth/token?grant_type=refresh_token&client_id=webtools&refresh_token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IjIwMjMuMTIuMjAta2V5In0.eyJhdGkiOiJEYkJfOHl5R2JuWTlrMnp0VFNOVUktT29nUkEiLCJzY29wZSI6WyJPUEVOIl0sImlzcyI6Imh0dHA6Ly9kc3MtaWFtLmRzcy1kZXYuc3ZjLmNsdXN0ZXIubG9jYWw6OTEwMCIsImV4cCI6MTc1NjIyOTY0MywianRpIjoibTdyaWNQM0pRcW43TnY5eC1xeXNYQ1REUkhVIiwiY2xpZW50X2lkIjoid2VidG9vbHMiLCJhdXRob3JpdGllcyI6WyJST0xFX2Jyb2tlciIsIlJPTEVfQURNSU4iXSwidXNlcm5hbWUiOiIyNzA1Nzc5NDYifQ.XlGlX-QrE4SDHFToIAJlXMYh8PpZ1Y9eNjYq1Atn07NQz5C9G95o3hDnzDR84vNhUv3wwiJNQKgf4IHgEw_sxNWif_tiYMvAAyeQnKUmkjZ5LEPeGVZophCYVYnBQK9NxCsTeDZmbU19qOHr1dIFDrkw-G875kPsvb7gazwUl2wEDFXVVhyxu9qxf5wO8tVzGMHwSuzyt4uhsF8YetNIne-kRSlmg6AQWyep4zzEqmso_YD6H7D_xO7xpR4IX8DMG8Qmu721m5LnyUJaS1B91a1PYcbH-QvT5cMDoQEKvIDttKtDgW6h9G7YeEnKOSGbjvbNyQlqxO4ABRqndompIw' \
  --header 'authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IjIwMjMuMTIuMjAta2V5In0.eyJzY29wZSI6WyJPUEVOIl0sImlzcyI6Imh0dHA6Ly9kc3MtaWFtLmRzcy1kZXYuc3ZjLmNsdXN0ZXIubG9jYWw6OTEwMCIsImV4cCI6MTc1NjIyOTU4MywianRpIjoiRGJCXzh5eUdiblk5azJ6dFRTTlVJLU9vZ1JBIiwiY2xpZW50X2lkIjoid2VidG9vbHMiLCJhdXRob3JpdGllcyI6WyJST0xFX2Jyb2tlciIsIlJPTEVfQURNSU4iXSwidXNlcm5hbWUiOiIyNzA1Nzc5NDYifQ.LKDH296SKl5SWES6D2eIRfnJo-bvbgjwRtYdWC4dc_1dfUyrk7vX1qiXxnqYUEQ5qjkSOIxxpr0NBHAT6FkacYJwhfOiXBOpOtuKcIui3OhbouSvmzh0KM9_o7Ey63rCmNeDa9ml6PMju8UYj50pRspcASZhNqRMaxH_i3TbHBs_HVOTwQaU-2MXGWpFaJ1ovc6uPNs4zonJ3GPn4ErSDJKXFDdXaNG_IFULcpZlZbJ4d_407L1_uxIud4bAMdpvzoI4Wac_fCYbdMDVbJb6CdevfLUlmDKXlSDxP84iI1rumdItGPTMH24HWB06TkH_lH8d3EEULOM-Q5Ouj5WRAg' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --header 'oauth_type: iam'
