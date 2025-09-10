{
          "type": "http",
          "name": "2 - POST Execute Script",
          "seq": 2,
          "request": {
            "url": "http://platform.dcloud.cl.bsch/executor/v1/execute?app-name=chl-dss-fraudlocal",
            "method": "POST",
            "headers": [],
            "params": [
              {
                "name": "app-name",
                "value": "chl-dss-fraudlocal",
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
              "mode": "bearer",
              "bearer": {
                "token": ""
              }
            }
          }
        },
        {
          "type": "http",
          "name": "3 - GET - Status",
          "seq": 3,
          "request": {
            "url": "http://platform.dcloud.cl.bsch/executor/v1/status/chl-dss-fraudlocal",
            "method": "GET",
            "headers": [
              {
                "name": "Content-Type",
                "value": "application/json",
                "enabled": true
              }
            ],
            "params": [],
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
              "mode": "bearer",
              "bearer": {
                "token": ""
              }
            }
          }
        },
        {
          "type": "http",
          "name": "4 - GET Generated Project",
          "seq": 4,
          "request": {
            "url": "http://platform.dcloud.cl.bsch/executor/v1/generated-project?app=chl-dss-fraudlocal",
            "method": "GET",
            "headers": [],
            "params": [
              {
                "name": "app",
                "value": "chl-dss-fraudlocal",
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
              "mode": "bearer",
              "bearer": {
                "token": "
              }
            }
          }
        },
